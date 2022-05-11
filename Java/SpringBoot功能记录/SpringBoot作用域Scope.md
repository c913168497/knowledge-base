
#### 描述
创建一个bean定义，其实质意义就是定义对应的类在 spring framework 中的配置值和依赖，以及作用域。
在 java 中的 类 有 private public 等关键字表示权限范围，那么在 spring framework中 也有类似定义配置的东西 那就是作用域

#### 总体阐述
|类别|说明|
|----|----|
|singleton| 在Spring IOC 容器中仅存在一个 Bean 实例， Bean 以单例方式存在 (作用域 默认值)
prototype| 每次从容器中调用Bean时，都返回一个新的实例，即每次调用getBean()时，都会 new XxxBean()
request |每次Http 请求都会新一个Bean，该作用域仅适用于WebApplicationContext 环境
session|同一个Http Session 共享一个Bean ,不同 Session 使用不同Bean,仅适用于 WebApplicationContext 环境
globalSession | 一般用于Portlet应用环境，同样仅适用于 WebApplicationContext 环境

#### 使用环境区别：
五种作用域中 request、 session 、global session 三种作用域仅在基于web的应用中使用（基于servelet 基本的 web 应用框架都存在）， 其它的两种只能用在基于web的Spring ApplicationContext 环境。

#### 细节描述
##### 1.singleton --- 唯一 bean 实例

当一个bean的作用域为singleton ,那么Spring IOC 容器中只会存在一个共享的bean实例，并且所有对bean的请求只要id与该bean定义相匹配，则只会返回bean的同一实例。

配置 lazy-init=”true” 
懒加载 ，到使用时才会进行初始化

代码
```
@Service
@Scope("singleton")
public class ServiceImpl{

}
```

##### 2.prototype --- 每次请求都会创建一个新的 bean 实例
当 bean 的作用域为 prototype，表示一个 bean 定义对应多个对象实例。  
prototype 作用域的 bean 让每次对 Bean 的请求(getBean) 产生一个新的对象实例

##### 3.request --- 每一次的 HTTP 请求都会产生一个新的 bean, 该 bean仅在当前 http request内有效  
request 只适用于 Web 程序， 每一次 HTTP 请求都会产生一个新的 bean, 同时该bean仅在当前http request 内有效，当请求结束后，该对象的生命周期即结束。

##### 4.session --- 每一次 HTTP 请求都会产生一个新的 bean ,该 bean 仅在当前 HTTP seesion 内有效
session 只适用于 Web 程序，每一次 HTTP 请求都会产生一个新的 bean,同时该bean仅在当前http seesion 内有效， session 最终被废弃时，session 作用域内的 bean 也会被废弃掉。

##### 5.glabalSession
这个用的少，glabalSession 类似于标准的 HTTP session 作用域，不过仅仅在基于 portlet 的 web 应用中才有意义。Portlet 规范定义了全局 Session 的概念，它被所有构成某个 portlet web 应用的各种不同的 portle t所共享。在global session 作用域中定义的 bean 被限定于全局portlet Session的生命周期范围内。



