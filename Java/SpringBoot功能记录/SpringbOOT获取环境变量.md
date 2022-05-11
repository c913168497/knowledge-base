```java
public class TestEnviroment implements EnvironmentAware {
     
   /**
    * 环境
    */
   private Environment environment;
   
   
   @Override
   public void setEnvironment(Environment environment) {
       this.environment = environment;
   }


   public void getVersion(){
       String version = environment.getProperty("dubbo.service.version");
   }

}

```