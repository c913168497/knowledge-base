记得在 resources 目录下增加 META-INF -> dubbo -> com.alibaba.dubbo.rpc.Filter
过滤器

```java
/**
 * 操作人信息过滤器
 *
 * @author chennengcheng
 * @date 2020-01-25
 */

@Activate(group = {CONSUMER, PROVIDER})
public class OperatorInfoFilter implements Filter {

    private final static String UPDATE_FIELD = "updateBy";

    private final static String INSERT_FIELD = "createBy";

    @Override
    public Result invoke(Invoker<?> invoker, Invocation invocation) throws RpcException {
        RpcContext context = RpcContext.getContext();
        Map<String, Object> objectAttachments = context.getObjectAttachments();
        if (Objects.isNull(objectAttachments.get(INSERT_FIELD)) || Objects.isNull(objectAttachments.get(UPDATE_FIELD))) {
            try {
                SecurityManager securityManager = ThreadContext.getSecurityManager();
                // 如果登陆了那么权限管理器会有信息
                if (Objects.nonNull(securityManager)) {
                    String username = ShiroUtils.getUsername();
                    context.setAttachment(INSERT_FIELD, username);
                    context.setAttachment(UPDATE_FIELD, username);
                }
            } catch (Exception e) {

            }
        }

        return invoker.invoke(invocation);
    }
}

```