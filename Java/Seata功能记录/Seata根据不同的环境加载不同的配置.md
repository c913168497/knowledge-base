
记得在resource 下增加 META-INF -> spring.factories 文件
内容, 这样就能自动加载
```java
org.springframework.context.ApplicationContextInitializer=\
com.ansun.config.config.SeataEnvironmentInitializer
```
```java

import io.seata.config.ConfigurationFactory;
import org.springframework.context.ApplicationContextInitializer;
import org.springframework.context.ConfigurableApplicationContext;
import org.springframework.core.env.ConfigurableEnvironment;

/**
 * 加载不同配置
 * @author chennengcheng
 * @date 2021-01-20 11:45
 * @remark
 */
public class SeataEnvironmentInitializer implements ApplicationContextInitializer<ConfigurableApplicationContext> {

    @Override
    public void initialize(ConfigurableApplicationContext applicationContext) {
        ConfigurableEnvironment environment = applicationContext.getEnvironment();
        if (environment.getActiveProfiles() != null  && environment.getActiveProfiles().length > 0) {
            String activeProfile = environment.getActiveProfiles()[0];
            System.setProperty(ConfigurationFactory.ENV_PROPERTY_KEY, activeProfile);
        }
    }
}
```