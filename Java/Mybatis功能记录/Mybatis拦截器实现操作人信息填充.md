
#### 从执行代码编译层面拦截
```java

/**
 * 操作人信息自动填充功能， 前提是 SQL 中 存在 updateBy 和 updateDate 和 createBy 和 createDate 这几个参数
 * 这个方法目的是为了填充手动实现的 sql
 *
 * @author chennengcheng
 * @date 2020-12-28
 */
@Intercepts({
        @Signature(type = Executor.class, method = "update", args = {MappedStatement.class, Object.class}),
})
@Component
public class OperatorInterceptorConfig implements Interceptor {

    private final static String UPDATE_FIELD = "updateBy";

    private final static String INSERT_FIELD = "createBy";

    private final static String INSERT_DATE_FIELD = "createDate";

    private final static String UPDATE_DATE_FIELD = "updateDate";

    @SuppressWarnings(value = "all")
    @Override
    public Object intercept(Invocation invocation) throws Throwable {
        Object[] args = invocation.getArgs();
        SqlCommandType sqlCommandType = null;
        for (Object arg : args) {
            if (arg instanceof MappedStatement) {
                MappedStatement ms = (MappedStatement) arg;
                sqlCommandType = ms.getSqlCommandType();
                if (sqlCommandType == SqlCommandType.INSERT || sqlCommandType == SqlCommandType.UPDATE) {
                    continue;
                }

                break;
            }

            if (arg instanceof Map) {
                Map<Object, Object> map = (Map<Object, Object>) arg;
                putProperty(map, sqlCommandType);
                for (Object value : map.values()) {
                    if (value instanceof Collection) {
                        for (Object e : ((Collection<Object>) value)) {
                            setProperty(e, sqlCommandType);
                        }
                    } else {
                        setProperty(value, sqlCommandType);
                    }
                }
            } else {
                setProperty(arg, sqlCommandType);
            }
        }
        return invocation.proceed();
    }

    private void putProperty(Map<Object, Object> map, SqlCommandType sqlCommandType) {
        RpcContext serverContext = RpcContext.getContext();
        String updateBy = serverContext.getAttachment(UPDATE_FIELD);
        String createBy = serverContext.getAttachment(INSERT_FIELD);
        Date now = new Date();
        if (SqlCommandType.INSERT == sqlCommandType && StringUtils.isNoneBlank(createBy)) {
            map.putIfAbsent(INSERT_FIELD, createBy);
            map.putIfAbsent(INSERT_DATE_FIELD, now);
        } else if (SqlCommandType.UPDATE == sqlCommandType && StringUtils.isNoneBlank(updateBy)) {
            map.putIfAbsent(UPDATE_FIELD, updateBy);
            map.putIfAbsent(UPDATE_DATE_FIELD, now);
        }
    }

    private void setProperty(Object obj, SqlCommandType sqlCommandType) {
        if (Objects.isNull(obj)) {
            return;
        }

        RpcContext serverContext = RpcContext.getContext();
        String updateBy = serverContext.getAttachment(UPDATE_FIELD);
        String createBy = serverContext.getAttachment(INSERT_FIELD);
        Date now = new Date();
        try {
            if (SqlCommandType.INSERT == sqlCommandType && StringUtils.isNoneBlank(createBy)) {
                BeanUtils.setProperty(obj, INSERT_FIELD, createBy);
                BeanUtils.setProperty(obj, INSERT_DATE_FIELD, now);
            } else if (SqlCommandType.UPDATE == sqlCommandType && StringUtils.isNoneBlank(updateBy)) {
                BeanUtils.setProperty(obj, UPDATE_FIELD, updateBy);
                BeanUtils.setProperty(obj, UPDATE_DATE_FIELD, now);
            }
        } catch (Exception e) {
            throw new BusinessException("填充修改人信息出错请检查：" + JSON.toJSONString(e));
        }
    }

    @Override
    public Object plugin(Object target) {
        if (target instanceof Executor) {
            return Plugin.wrap(target, this);
        }

        return target;
    }

    @Override
    public void setProperties(Properties properties) {
    }
}
```

#### 从框架层面自动填充

```java
/**
 * 操作人信息自动填充功能，mybatis 框架自动填充数据
 * 这个方法目的是为了填充 框架方法
 *
 * @author chennengcheng
 * @date 2020-12-28
 */

@Primary
@Component
public class OperatorMetaObjectHandlerConfig implements MetaObjectHandler {

    private final static String UPDATE_FIELD = "updateBy";

    private final static String INSERT_FIELD = "createBy";

    private final static String INSERT_DATE_FIELD = "createDate";

    private final static String UPDATE_DATE_FIELD = "updateDate";

    @Override
    public void insertFill(MetaObject metaObject) {
        RpcContext serverContext = RpcContext.getContext();
        String createBy = serverContext.getAttachment(INSERT_FIELD);
        Date now = new Date();
        this.setFieldValByName(INSERT_FIELD, createBy, metaObject);
        this.setFieldValByName(INSERT_DATE_FIELD, now, metaObject);
        this.setFieldValByName(UPDATE_FIELD, createBy, metaObject);
        this.setFieldValByName(UPDATE_DATE_FIELD, now, metaObject);
    }

    @Override
    public void updateFill(MetaObject metaObject) {
        RpcContext serverContext = RpcContext.getContext();
        String updateBy = serverContext.getAttachment(UPDATE_FIELD);
        this.setFieldValByName(UPDATE_FIELD, updateBy, metaObject);
        this.setFieldValByName(UPDATE_DATE_FIELD, new Date(), metaObject);
    }
}
```
#### 还要记得，mybatis 这里填充默认值存在bug，需要重新写 setDefaultFieldVal 方法
```java

/**
 * 自动填充表字段值Handler
 *
 * @author liliang
 * @date 2019-06-05 15:53
 * @remark
 */
@Component
public class MyMetaObjectHandler implements MetaObjectHandler {

    @Override
    public void insertFill(MetaObject metaObject) {
        this.setDefaultFieldVal("createBy", "admin", metaObject, FieldFill.INSERT); //TODO:获取当前用户名
        this.setInsertFieldValByName("createDate", new Date(), metaObject);
        this.setDefaultFieldVal("updateBy", "admin", metaObject, FieldFill.INSERT);
        this.setInsertFieldValByName("updateDate", new Date(), metaObject);
    }

    @Override
    public void updateFill(MetaObject metaObject) {
        this.setDefaultFieldVal("updateBy", "admin", metaObject, FieldFill.UPDATE);
        this.setUpdateFieldValByName("updateDate", new Date(), metaObject);
    }

    private void setDefaultFieldVal(String fieldName, String fieldVal, MetaObject metaObject, FieldFill fieldFill){
        if (Objects.nonNull(fieldVal)) {
            if (metaObject.hasSetter(fieldName) && metaObject.hasGetter(fieldName) && this.isFill(fieldName, fieldVal, metaObject, fieldFill)) {
                Object value = metaObject.getValue(fieldName);
                if (Objects.isNull(value)) {
                    metaObject.setValue(fieldName, fieldVal);
                }

            } else if (metaObject.hasGetter("et")) {
                Object et = metaObject.getValue("et");
                if (et != null) {
                    MetaObject etMeta = SystemMetaObject.forObject(et);
                    if (etMeta.hasSetter(fieldName) && this.isFill(fieldName, fieldVal, etMeta, fieldFill)) {
                        if (etMeta.hasGetter(fieldName)) {
                            Object value = etMeta.getValue(fieldName);
                            if (Objects.isNull(value)) {
                                etMeta.setValue(fieldName, fieldVal);
                            }
                        }
                    }
                }
            }
        }
    }

}

```