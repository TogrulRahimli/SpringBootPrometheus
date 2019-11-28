
## Integration Prometheus to SpringBoot and Monitoring with Prometheus and Grafana

#### Add following dependencies to pom.xml

```maven
<!-- Spring Boot Actuator -->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>

<!-- Micrometer Prometheus registry  -->
<dependency>
    <groupId>io.micrometer</groupId>
    <artifactId>micrometer-registry-prometheus</artifactId>
</dependency>
```

#### Then,you must be add below configuration to your application.yml (this configs maybe changes according to your wishes)

```maven
management:
  endpoints:
    web:
      base-path: /management
      exposure:
        include: ["health", "info", "env" , "metrics" , "prometheus"]
  endpoint:
    health:
      enabled: true
      show-details: always
    metrics:
      export:
        # Prometheus is the default metrics backend
        prometheus:
          enabled: true
          step: 60
      binders:
        jvm:
          enabled: true
        processor:
          enabled: true
        uptime:
          enabled: true
        logback:
          enabled: true
        files:
          enabled: true
        integration:
          enabled: true
      distribution:
        percentiles-histogram:
          all: true
        percentiles:
          all: 0, 0.5, 0.75, 0.95, 0.99, 1.0
      web:
        server:
          auto-time-requests: true
    prometheus:
      enabled: true
```

#### Then, you’ll start seeing to your metrics with below urls
Metrics url  --  http://localhost:8080/management/prometheus

#### Pull prometheus image
```
docker pull prom/prometheus
```

#### We need to configure Prometheus to scrape metrics data /prometheus endpoint.Create a new file called prometheus.yml(in resource folder) with the following configurations. Please make sure to replace the HOST_IP with the IP address of the your computer where your Spring Boot application is running. Note that, localhost won’t work here because we’ll be connecting to the HOST machine from the docker container. You must be specify the network IP address.

```
# my global config
global:
  scrape_interval:     15s # Set the scrape interval to every 15 seconds. Default is every 1 minute.
  evaluation_interval: 15s # Evaluate rules every 15 seconds. The default is every 1 minute.
  # scrape_timeout is set to the global default (10s).

# A scrape configuration containing exactly one endpoint to scrape:
# Here it's Prometheus itself.
scrape_configs:
  - job_name: 'prometheus'
    static_configs:
      - targets: ['127.0.0.1:9090']      #this ip and port belong to currently running prometheus

  - job_name: 'the-name-you-want'
    metrics_path: '/management/prometheus'
    scrape_interval: 5s
    static_configs:
      - targets: ['your-comp-ip:your-application-port']

```

#### Running prometheus using Docker
```
$ docker run -d --name=prometheus -p 9090:9090 -v C:\Users\RehimliTF\IdeaProjects\Example-Projects\SpringBootPrometheus\prometheus.yml:/etc/prometheus/prometheus.yml prom/prometheus --config.file=/etc/prometheus/prometheus.yml
```

#### Look at browser http://localhost:9090 prometheus dashboard and must be write prometheus queries to Expression field.You can see prometheus queries in this site  https://prometheus.io/docs/practices/naming/

```
docker run -d --name=grafana -p 3000:3000 grafana/grafana
```
#### Look at browser http://localhost:3000 Grafana
#### Add the Prometheus data source in Grafana
#### Create datasourse and select prometheus type,
  Name = Prometheus , url = http://localhost:9000
  If access default(server) not worked then select browser access type.

#### Create new dashboard and add query
   select prometheus query type and add prometheus queries    


