# wiremock-metrics

Originally forked from https://github.com/mikeskali/wiremock-metrics

Since it creates a fatJar, all dependencies are packed, including WireMock. Note that this repo has an updated version of the build.gradle to make it compatible with Gradle 8. Also, the WireMock version has been updated to the latest one that may be compiled with this extension (newer versions seem not to be found by gradle/mvn?)

The way this library is started in Standalon CLI is updated

Extension to add [Prometheus](https://prometheus.io/) telemetry support to [Wiremock](https://github.com/tomakehurst/wiremock).
This repo utilized `PostServeAction` to record all requests and `AdminApiExtention` to expose metrics in Prometheus format (under `/admin__metrics`)

## Metrics
| metric name            | description                                           | tags                                                                                                      |
|------------------------|-------------------------------------------------------|-----------------------------------------------------------------------------------------------------------|
| requestTimer_ms_count  | count of requests                                     | path - url stub, pattern status - http status, method - http method                                       |
| requestTimer_ms_sum    | summary of latency in milliseconds                    | path - url stub, pattern status - http status, method - http method                                       |
| requestTimer_ms_bucket | Latency buckets, to be used with percentile functions | le - less equal, path - url stub pattern, status - http status, method - http method |
| wiremock_metrics_errors_total| Errors while calculating metrics                      | errType - error type | 
## Build
`gradle clean fatJar`
Ready to use jar will be under build/libs

## Use
after creating the jar above, you can run wiremock with prometheus telemetry.

### Code
`WireMockServer server = new WireMockServer(conf.port(8092).extensions(new PrometheusExporterExtension(), new MetricsExtension()));`

### Standalone (CLI)
add the generated jar to some dir (/WIREMOCK-EXTENSIONS-PATH/lib/)
`java -cp /WIREMOCK-EXTENSIONS-PATH/lib/<generated-jarfile.jar> com.github.tomakehurst.wiremock.standalone.WireMockServerRunner --extensions=wiremock.PrometheusExporterExtension,wiremock.MetricsExtension` [<other-options>]

prometheus endpoint will be exposed as `/__admin/metrics`

## Grafana Dashboard
You can import the wiremock-dashboard.json, located in grafana folder. 

## Containerized version
Kubernetes (Red Hat OpenShift) ready version at https://quay.io/repository/avitui/wiremock_metrics
