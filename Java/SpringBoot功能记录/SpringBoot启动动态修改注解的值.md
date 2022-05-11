```
 Field[] declaredFields = beanClazz.getDeclaredFields();
 for (Field declaredField : declaredFields) {
        declaredField.setAccessible(true);
        Class<? extends Annotation> referenceClazz = (Class<? extends Annotation>) Class.forName("org.apache.dubbo.config.annotation.Reference");
        Object reference = declaredField.getAnnotation(referenceClazz);
        if (Objects.nonNull(reference)) {
           InvocationHandler invocationHandler = Proxy.getInvocationHandler(reference);
        try {
            Field field = invocationHandler.getClass().getDeclaredField("memberValues");
            field.setAccessible(true);
            Map<String, Object> memberValues = (Map<String, Object>) field.get(invocationHandler);
            memberValues.put("version", versionNew);
        } catch (NoSuchFieldException | IllegalAccessException e) {
            e.printStackTrace();
        }
        }
 }
   
```
