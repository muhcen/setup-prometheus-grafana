# setup grafana and prometheus with nestjs

in this page we learn setup grafana and prometheus in nestjs

# fundamental

***Prometheus***: Prometheus is a monitoring solution for storing time series data like metrics.

***Grafana***: Grafana allows to visualize the data stored in Prometheus (and other sources).
## Installation
first we should install grafana and prometheus in computer depending on os,
or we can use docker image for use this tools.
``in the docker-compose.yml file we run this tools.``

in the `docker-compose.yml` we run 3 service first run grafana for monitoring, seconde run prometheus for extract data from application and Third run application.



## connect prometheus and app
 in the `prometheus/prometheus.yml` file we home configuration for connect prometheus to app.


```
global:
  scrape_interval: 5s
scrape_configs:
  - job_name: "example-nodejs-app"
    static_configs:
      - targets: ["core-app:8080"]
```
in targets we set application name and port.

scrape_interval(`the time between each Prometheus scrape`).

## run application
```
docker compose up
```

## application
in the app we should install `prom-client` `@willsoto/nestjs-prometheus` package for work with prometheus. 

`prom-client`: it provides the building blocks to export metrics to Prometheus via the pull and push methods and supports all Prometheus metric types such as histogram, summaries, gauges and counters.

`@willsoto/nestjs-prometheus`: By default, this will register a /metrics endpoint that will return the default metrics.

in the app we have interceptor for log http request that received by the application.

***use*** `@willsoto/nestjs-prometheus` in application: 
```
import { Module } from '@nestjs/common';
...
import { PrometheusModule } from '@willsoto/nestjs-prometheus';
...

@Module({
  imports: [
      PrometheusModule.register() // setup promethus module in app
  ],
  controllers: [...],
  providers: [...],
})
export class CoreModule {}
```


in the interceptor create simple counter for counting number of /metrics request.
```
  ...
  private counter = new promClient.Counter({
    name: 'number_of_metrics_request',
    help: 'metric_help',
  });
  ...
  ...
  this.counter.inc(); // every time request received by the application
  ...                 // number_of_metrics_request incremented
  ...
```  

## prometheus
after run app go to the http://localhost:9090/targets and the core-app should be ``up``.

## grafana
now go to the http://localhost:5000 and select dataSource(`any place from which Grafana can pull data and use this data for visualization`) for configuration:

> default username and password is admin and admin.


1: first select prometheus.

2: copy prometheus url(`http://localhost:9090`) in the url.

3: set browser in access.

4: click save and test.

5: go back to the dashboard and click add new panel.

6: in the query section we should set to default or prometheus(`it is same`)

7: now we can select metrics.

congratulations!