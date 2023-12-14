# OpenTelemetry on local cluster try/demo

## Setup

Setup: Ubundu 22.04 LTS

Install & configure:
  - docker
  - kind
  - helm

## running

Start the cluster:

I use `unkind` as cluster name in this example

```
kind create cluster --name unkind
kind export kubeconfig --name unkind


```
helm repo add open-telemetry https://open-telemetry.github.io/opentelemetry-helm-charts
helm install my-otel-demo open-telemetry/opentelemetry-demo`
```


Port-forward
```
kubectl port-forward --address 0.0.0.0 services/my-otel-demo-grafana 3000:80
```


Run demo
```
helm install otel -f collector-values.yaml open-telemetry/opentelemetry-collector
```

Delete helm
```
helm list --all-namespaces
helm delete otel --namespace otel
```
