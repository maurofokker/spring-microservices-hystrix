# Spring Microservice Hystrix

* Part of [microservice demo](https://github.com/maurofokker/microservices-demo)
* and [simple microservice](https://github.com/maurofokker/spring-microservices-simple-service)

* Create a simple rest to consume simple microservice described above

* In case a target service present a failure, hystrix provide a circuit breaker feature
* Failures in a microservice architecture can cascade and cause bigger problems
* Main feature of `Hystrix` is being able to provide a graceful fail over within a micro service architecture. [Reference](https://github.com/Netflix/Hystrix/wiki)
* If service fail then a default value can be provide to the source... this is provide by `@EnableCircuitBreaker` annotation
  * this allow to route our execution to a different method in the case of a failure using the annotation method `@HystrinxCommand`
    ```java
      @RequestMapping("/simpleServiceClient")
      @HystrixCommand(fallbackMethod = "failover")
      public List<String> simpleServiceClient() {
        return this.restTemplate.getForObject("http://localhost:8888/service", List.class);
      }
    
      public List<String> failover() {
        return Arrays.asList("default_1", "default_2");    
      }
    ```
    * `fallbackMethod` allow to specify a particular method to invoke in case client method call fails
    * when `http://localhost:8888/service` fails then `failover()` method will be called
    * fallback method should have same parameters than source method
  * Properties in `@HystrixCommand` can be modified using `commandProperties` element. [Reference](https://github.com/Netflix/Hystrix/wiki/Configuration)

## Hystrix Dashboard

* enable an interface to manage and monitor circuits breaking in micro services  in order to visualize state in the distributed system
* starter pom dependency
  ```xml
    <dependency>
      <groupId>org.springframework.cloud</groupId>
      <artifactId>spring-cloud-starter-netflix-hystrix-dashboard</artifactId>
    </dependency>
  ``` 
* replace `@EnableCircuitBreaker` for `@EnableHystrix` annotation that is more generic and get a hystrix stream 
* enable `@EnableHystrixDashboard` annotation
* enable `management.endpoints.web.exposure.include=hystrix.stream` stream via [actuator](http://cloud.spring.io/spring-cloud-netflix/single/spring-cloud-netflix.html#_hystrix_metrics_stream)
  * endpoint `http://localhost:8080/actuator/hystrix.stream` will show traffic generated in services as json
* endpoint `http://localhost:8080/hystrix` will show the dashboard
  * inside can subscribe with `http://localhost:8080/actuator/hystrix.stream` url and get a visual representation of circuit breaking 
