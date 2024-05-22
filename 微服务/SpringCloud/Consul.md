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

### 3.1 服务配置

- 引入依赖

  ```xml
  <!--SpringCloud consul config-->
  <dependency>
      <groupId>org.springframework.cloud</groupId>
      <artifactId>spring-cloud-starter-consul-config</artifactId>
  </dependency>
  <dependency>
      <groupId>org.springframework.cloud</groupId>
      <artifactId>spring-cloud-starter-bootstrap</artifactId>
  </dependency>
  ```

- 新建bootstrap.yml配置文件，并修改application.yml文件

  ```yaml
  # bootstrap.yml
  spring:
    application:
      name: cloud-payment-service
    cloud:
      consul:
        host: localhost
        port: 8500
        discovery:
          service-name: ${spring.application.name}
        config:
          profile-separator: '-'  # default value is ","，we update '-'
          format: YAML # 与data数据类型一致
  
  
  # application.yml
  spring:
    profiles:
      active: dev # 多环境配置加载内容dev/prod,不写就是默认default配置
  ```

- Consul服务器上填写相关key/value键值对 ([配置规则](https://docs.spring.io/spring-cloud-consul/reference/config.html))

  - 新建config、cloud-provider-pay (微服务名称，默认配置)、cloud-provider-pay-dev (开发环境配置)、cloud-provider-pay-prod (生产环境配置) 文件夹：

  ![image-20240521202805815](D:\study\KK-Notes\微服务\SpringCloud\assets\image-20240521202805815.png)

  - 在三个文件夹下分别创建data (key, 键的名称不可修改)，及相应的value

    ![image-20240521203815972](D:\study\KK-Notes\微服务\SpringCloud\assets\image-20240521203815972.png)

  - 测试

    ```java
    @RestController
    @Slf4j
    @RequestMapping("/test/consul/config")
    public class ConsulConfigTestController {
    
        @Value("${server.port}")
        String port;
    
        @GetMapping("/info")
        public ResultData<String> getConfigInfoFromConsul(@Value("${consul.info}") String info) {
            return ResultData.success("port: " + port + " -> "
                    + "consul.info: " + info);
        }
    }
    ```

    ![image-20240521205204293](D:\study\KK-Notes\微服务\SpringCloud\assets\image-20240521205204293.png)

    (注：获得的是dev信息，与application.yml中的profiles.active值一直)

### 3.2 动态刷新

动态刷新：即在Consul服务器上修改data的内容后，保证可以及时刷新到各个微服务模块。

- 在主启动类上添加动态刷新注解

  ```java
  @RefreshScope // Consul动态刷新
  ```

- 修改默认刷新时间

  ```yaml
  consul:
  	config:
  		watch:
  			wait-time: 1 # 默认55s刷新一次数据
  ```



## 四、持久化配置

**以上的步骤，consul重启后，会丢失调之前配置的信息，因此需要配置Consul的持久化存储。**

- **windows**下配置：在consul安装目录下新建 data 文件夹和 consul_start.bat 脚本文件 (以管理员身份运行)

  ```bash
  # consul_start.bat
  @echo.服务启动......  
  @echo off  
  @sc create Consul binpath= "D:\software\consul\consul.exe agent -server -ui -bind=127.0.0.1 -client=0.0.0.0 -bootstrap-expect  1  -data-dir D:\software\consul\data   "
  @net start Consul
  @sc config Consul start= AUTO  
  @echo.Consul start is OK......success
  @pause
  ```

  









