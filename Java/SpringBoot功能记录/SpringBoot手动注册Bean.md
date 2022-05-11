第一种 new Bean 方式处理
```
import org.springframework.beans.factory.BeanFactory;

public class TestBeanFactory implements BeanFactoryAware{
   private BeanFactory beanFactory;
   
   
    @Override
    public void setBeanFactory(BeanFactory beanFactory) throws BeansException {
        this.beanFactory = beanFactory;
    }
    
    public void registerSingletonBean(){
        // bean 对象
        CacheManager cacheManager = initCacheManager(cacheKeyTime, TimeUnit.MINUTES);
        ConfigurableBeanFactory configurableBeanFactory = (ConfigurableBeanFactory) beanFactory;
        // 注册
        configurableBeanFactory.registerSingleton(beanName, cacheManager);
    }
    
    
    private CacheManager initCacheManager(String cacheKeyTime, TimeUnit unit){
        //小时
        CaffeineCacheManager cacheManager = new CaffeineCacheManager();
        // 定制化缓存Cache
        String duration = cacheKeyTime.substring(0, cacheKeyTime.length() - 1);
        cacheManager.setCaffeine(Caffeine.newBuilder()
                .expireAfterWrite(Long.parseLong(duration), unit)
                .initialCapacity(100)
                .maximumSize(10_000));
        return cacheManager;
    }
}
```

第二种 直接通过 Class 方式处理
```
public class TestBeanDefinitionRegistrar implements ImportBeanDefinitionRegistrar {
    
    @Override
    public void registerBeanDefinitions(AnnotationMetadata importingClassMetadata, BeanDefinitionRegistry registry) {
            Class<?> beanClazz = XXXX;
            BeanDefinitionBuilder builder = BeanDefinitionBuilder.genericBeanDefinition(beanClazz);
            GenericBeanDefinition definition = (GenericBeanDefinition) builder.getRawBeanDefinition();
            definition.setBeanClass(beanClazz);
            definition.setAutowireMode(GenericBeanDefinition.AUTOWIRE_BY_TYPE);
            String beanId = beanClazz.getName();
            // 使用类路径进行注册
            registry.registerBeanDefinition(beanId, definition);
    }
    
}
```

最后记得用 import 导入
