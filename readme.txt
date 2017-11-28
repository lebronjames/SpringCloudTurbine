SpringCloud断路器聚合监控(Hystrix Turbine)集成
一、pom.xml增加turbine依赖spring-cloud-starter-turbine、spring-cloud-netflix-turbine。

二、主类增加@EnableTurbine注解
@EnableTurbine
@SpringBootApplication
public class SpringCloudTurbineApplication {
	public static void main(String[] args) {
		SpringApplication.run(SpringCloudTurbineApplication.class, args);
	}
}

三、配置文件
spring:
  application.name: service-turbine
server:
  port: 8769
security.basic.enabled: false
turbine:
  aggregator:
    clusterConfig: default   # 指定聚合哪些集群，多个使用","分割，默认为default。可使用http://.../turbine.stream?cluster={clusterConfig之一}访问
  appConfig: RIBBON-HYSTRIX-CONSUMER-DASHBOARD,FEIGN-HYSTRIX-CONSUMER-DASHBOARD  ### 配置Eureka中的serviceId列表，表明监控哪些服务
  clusterNameExpression: new String("default")
  # 1. clusterNameExpression指定集群名称，默认表达式appName；此时：turbine.aggregator.clusterConfig需要配置想要监控的应用名称
  # 2. 当clusterNameExpression: default时，turbine.aggregator.clusterConfig可以不写，因为默认就是default
  # 3. 当clusterNameExpression: metadata['cluster']时，假设想要监控的应用配置了eureka.instance.metadata-map.cluster: ABC，则需要配置，同时turbine.aggregator.clusterConfig: ABC
eureka:
  client:
    serviceUrl:
      defaultZone: http://10.5.2.241:1111/eureka/

四、依次启动监控实例，监控
