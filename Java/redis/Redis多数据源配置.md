

#### RedisConfig
```
import org.springframework.beans.factory.annotation.Value;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.data.redis.connection.RedisConnectionFactory;
import org.springframework.data.redis.connection.jedis.JedisConnectionFactory;
import org.springframework.data.redis.core.RedisTemplate;
import org.springframework.data.redis.core.StringRedisTemplate;
import redis.clients.jedis.JedisPoolConfig;

import java.io.Serializable;


@Configuration
public class RedisCfg {

	@Value("${spring.redis.host}")
	private String hostName;
	@Value("${spring.redis.password}")
	private String password;
	@Value("${spring.redis.pool.max-idle}")
	private Integer maxIdle;
	@Value("${spring.redis.pool.max-active}")
	private Integer maxTotal;
	@Value("${spring.redis.pool.max-wait}")
	private Long maxWait;
	@Value("${spring.redis.pool.min-idle}")
	private Integer minIdle;
	@Value("${spring.redis.port}")
	private Integer port;
	@Value("${spring.redis.ssl}")
	private Boolean ssl;
	@Value("${spring.redis.timeout}")
	private Integer timeout;


	@Bean("redisTemplate")
	RedisTemplate<String, String> redisTemplate() {
		return getDatabase(0);
	}

	@Bean("redisTemplateTwelve")
	RedisTemplate<String, String> redisTemplateTwelve() {
		return getDatabase(12);
	}

	@Bean("redisSerializableTemplate")
	RedisTemplate<Serializable, Serializable> redisSerializableTemplate(RedisConnectionFactory connectionFactory) {
		RedisTemplate<Serializable, Serializable> redisTemplate = new RedisTemplate<>();
		redisTemplate.setConnectionFactory(connectionFactory);
		return redisTemplate;
	}

	private RedisTemplate<String, String> getDatabase(int index){
		JedisConnectionFactory jedis = new JedisConnectionFactory();
		jedis.setHostName(hostName);
		jedis.setPort(port);
		jedis.setPassword(password);
		jedis.setTimeout(timeout);
		jedis.setUseSsl(ssl);
		jedis.setDatabase(index);
		jedis.setPoolConfig(this.poolConfig());
		jedis.afterPropertiesSet();
		return new StringRedisTemplate(jedis);
	}

	private JedisPoolConfig poolConfig() {
		JedisPoolConfig poolConfig = new JedisPoolConfig();
		poolConfig.setMaxIdle(maxIdle);
		poolConfig.setMaxTotal(maxTotal);
		poolConfig.setMaxWaitMillis(maxWait);
		poolConfig.setMinIdle(minIdle);
		return poolConfig;
	}

}


```

#### Spring 配置文件
```
spring:
   redis:
        database: 0
        host: r-wz9be8zazv1ifzida5.redis.rds.aliyuncs.com
        password: Jokeaq1sw2de
        ssl: false
        pool:
            max-active: 100
            max-idle: 100
            max-wait: -1
            min-idle: 0
        port: 6379
        timeout: 0
```

#### maven 
```
<!-- spring boot redis -->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-redis</artifactId>
</dependency>
```