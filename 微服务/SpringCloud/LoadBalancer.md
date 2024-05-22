# LoadBalancer

[Spring Cloud LoadBalancer :: Spring Cloud Commons](https://docs.spring.io/spring-cloud-commons/reference/spring-cloud-commons/loadbalancer.html)

## 一、客户端负载 vs 服务器端负载

- **Nginx**：用于服务器负载均衡，客户端所有请求都会交给 nginx，然后由 nginx 实现转发请求。
- **Loadbalancer**：用于本地负载均衡，在调用微服务接口时候，会在 Consul 上获取注册信息服务列表之后缓存到 JVM 本地，从而在本地实现 RPC 远程服务调用技术。

## 二、工作原理

- 从 Consul Server 中查询并拉取相关微服务列表；
- 按照指定的**负载均衡策略**从拉取到的服务注册列表中由客户端自己选择一个地址。

![image-20240522154134248](D:\study\KK-Notes\微服务\SpringCloud\assets\image-20240522154134248.png)

## 三、案例

- 创建两个一样的微服务模块作为服务提供者

  ![image-20240522162357748](D:\study\KK-Notes\微服务\SpringCloud\assets\image-20240522162357748.png)

- 服务消费者模块配置：

  - 引入 LoadBalancer 依赖

    ```xml
    <!--loadbalancer-->
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-loadbalancer</artifactId>
    </dependency>
    ```

  - 添加测试接口，会发现 LoadBalancer 以**轮询策略**调用服务提供者的接口

    ```java
    private static final String 
        PaymentSrv_URL = "http://cloud-provider-pay-service";//Consul中的服务名称
    
    @GetMapping("/pay/get/consul/config/info")
    public ResultData getConsulConfigInfo(){
        return restTemplate.getForObject(
            PaymentSrv_URL + "/test/consul/config/info", ResultData.class);
    }
    ```

    ![image-20240522162434779](D:\study\KK-Notes\微服务\SpringCloud\assets\image-20240522162434779.png)

    ![image-20240522162451993](D:\study\KK-Notes\微服务\SpringCloud\assets\image-20240522162451993.png)

- 消费者获取 Consul 上所注册微服务的原理：

  ```java
     @Resource
      private DiscoveryClient discoveryClient;
      @GetMapping("/discovery/service")
      public ResultData discovery()
      {
          List<String> services = discoveryClient.getServices();
          for (String element : services) {
              System.out.println(element);
          }
  
          System.out.println("===================================");
  
          List<ServiceInstance> instances = discoveryClient.getInstances("cloud-provider-pay-service");
          for (ServiceInstance element : instances) {
              System.out.println(element.getServiceId()+"\t"+element.getHost()+"\t"+element.getPort()+"\t"+element.getUri());
          }
  
          return ResultData.success("success");
      }
  ```

  

## 四、负载均衡算法

默认有两种算法：

- 轮询算法

  ```java
  public class RoundRobinLoadBalancer implements ReactorServiceInstanceLoadBalancer {}
  
  /* 算法原理：
  实际调用服务器位置下标 = rest接口第几次请求数 % 服务器集群总数量
  (每次服务重启动后rest接口计数从1开始)
  **/
  ```

- 随机算法

  ```java
  public class RandomLoadBalancer implements ReactorServiceInstanceLoadBalancer {}
  ```

- 修改消费者的负载均衡算法

  ```java
  @Configuration
  @LoadBalancerClient(value = "cloud-provider-pay-service",configuration = RestTemplateConfig.class) //value值与consul里面微服务的名字一致
  public class RestTemplateConfig {
      @Bean
      @LoadBalanced
      public RestTemplate restTemplate()
      {
          return new RestTemplate();
      }
  
      @Bean
      ReactorLoadBalancer<ServiceInstance> randomLoadBalancer(Environment environment,
                                                              LoadBalancerClientFactory loadBalancerClientFactory) {
          String name = environment.getProperty(LoadBalancerClientFactory.PROPERTY_NAME);
  
          return new RandomLoadBalancer(loadBalancerClientFactory.getLazyProvider(name, ServiceInstanceListSupplier.class), name);
      }
  }
  ```





