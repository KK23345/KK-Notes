# Cousul

## 一、安装

[Install | Consul | HashiCorp Developer](https://developer.hashicorp.com/consul/install?product_intent=consul#windows)

```bash
# 以开发模式启动
consul agent -dev # 访问consul首页 http://localhost:8500
```

## 二、服务注册与发现

[Quick Start :: Spring Cloud Consul](https://docs.spring.io/spring-cloud-consul/reference/quickstart.html)

- 引入依赖

  ```xml
  <!--SpringCloud consul discovery -->
  <dependency>
      <groupId>org.springframework.cloud</groupId>
      <artifactId>spring-cloud-starter-consul-discovery</artifactId>
      <exclusions>
          <exclusion>
              <groupId>commons-logging</groupId>
              <artifactId>commons-logging</artifactId>
          </exclusion>
      </exclusions>
  </dependency>
  ```

- 修改配置文件

  ```yaml
  spring: 
  	application:
      	name: cloud-provider-pay-service
      cloud:
          consul:
            host: localhost
            port: 8500
            discovery:
              service-name: ${spring.application.name}
  ```

- 修改启动类，开启服务发现

  ```java
  @EnableDiscoveryClient // 在启动类上添加注解
  ```

- 若Controller需要调用其他微服务API，修改URL地址和RestTemplateConfig配置类

  ```java
  public static final String URL = "http://cloud-provider-pay-service"; //Consul中的服务名称
  
  @Configuration
  public class RestTemplateConfig {
      @Bean
      @LoadBalanced // 添加允许负载均衡注解，因为引入微服务后，默认同时间会有多条API调用
      public RestTemplate restTemplate()
      {
          return new RestTemplate();
      }
  }
  ```

## 三、服务配置与刷新

