# Tutorial

* [Port Forwarding](#port-forwarding)
* [Grafana](#grafana)
* [Fluent Bit](#fluent-bit)
* [Otel](#otel)
* [Prometheus](#prometheus)
* [Traces](#traces)
* [Pixie](#pixie)

## Port Forwarding

Ensure that you are forwarding ports from the frontend proxy.
The frontend is a e-commerce website which sells telescopes and should be accessible
on port 8080.

Reminder, to port forward, run the following:

```console
kubectl port-forward -n otel-demo svc/otel-demo-frontendproxy 8080:8080
```

## Grafana

1. Grafana should be accessible at [http://localhost:8080/grafana](http://localhost:8080/grafana)

1. To setup the Pixie datasource within Grafana, navigate to the [Pixie datasource config page](http://localhost:8080/grafana/datasources/edit/pixie)

1. Use the Pixie CLI to get an API key for your account

    ```console
    px api-key create --short
    ```

1. Paste the newly created API key into the [Pixie datasource config page](http://localhost:8080/grafana/datasources/edit/pixie) within Grafana

1. Click `Save and Test` to ensure that the key is saved and works as expected

## Fluent Bit

1. Navigate to the [Grafana dashboards page](http://localhost:8080/grafana/dashboards)

1. Click the `Fluentbit Logs` link to explore the logs being collected

1. There seem to be a lot of logs here, perhaps we want to filter down some of the logs that are of less interest to us

1. We can check the our configuration for Fluent Bit by querying the kubernetes configmap

    ```console
    kubectl get configmap -n otel-demo otel-demo-fluent-bit -o "jsonpath={.data['fluent-bit\.conf']}"
    ```

1. Perhaps we should add a filter to the configmap so that Fluent Bit drops logs from the `loki-canary`

1. Open the configmap in an editor using

    ```console
    kubectl edit configmap -n otel-demo otel-demo-fluent-bit
    ```

1. Add the following filter to drop some more logs

    ```text
    [FILTER]
    Name grep
    Match kube.*
    Exclude kubernetes_container_name loki-canary
    ```

## Otel

1. Parts of the demo e-commerce website have been instrumented with Open Telemetry

1. Here's an example of such an instrumentation callsite in the recommendation services
    * The metric is setup at [src/recommendationservice/metrics.py#L8-L19](https://github.com/open-telemetry/opentelemetry-demo/blob/deaf1f611f50ba6effdfc03a2ad2adb8a4595d7f/src/recommendationservice/metrics.py#L8-L19)
    * The metric is collected [src/recommendationservice/recommendation_server.py#L48-L49](https://github.com/open-telemetry/opentelemetry-demo/blob/deaf1f611f50ba6effdfc03a2ad2adb8a4595d7f/src/recommendationservice/recommendation_server.py#L48-L49)

## Prometheus

1. Navigate to the [Grafana dashboards page](http://localhost:8080/grafana/dashboards)

1. Click the `Prometheus Dashboard` link to explore promethues metrics

1. Click through the various services in the dropdown in the top left to explore metrics for the various services

1. Naviagate to the [feature flag](http://localhost:8080/feature) page to enable `productCatalogFailure`

1. This should trigger failures in the app, can you use the `Prometheus Dashboard` to find the issue

1. Hint: Select the `frontend` service in the `Prometheus Dashboard`

## Traces

1. Navigate to the [Grafana dashboards page](http://localhost:8080/grafana/dashboards)

1. Click the `Traces Dashboard` link to explore traces

1. Can you figure out which product is causing errors in the e-commerce website

1. Hint: Use the `error=true` filter to filter traces

## Pixie

1. Navigate to the [Grafana dashboards page](http://localhost:8080/grafana/dashboards)

1. Click through then various Pixie dashboards to explore data collected by Pixie

1. You can also checkout the [Pixie UI](https://work.withpixie.ai) to explore a wider variety of dashboards that Pixie provides
