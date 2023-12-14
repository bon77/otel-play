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

### Kind
```
kind create cluster --name unkind
kind export kubeconfig --name unkind
```

### Helm
#### Initial
```
helm repo add open-telemetry https://open-telemetry.github.io/opentelemetry-helm-charts
helm install my-otel-demo open-telemetry/opentelemetry-demo`
```
#### Install
```
helm install otel --namespace otel -f collector-values.yaml open-telemetry/opentelemetry-collector
```

#### Cleanup
```
helm list --all-namespaces
helm delete otel --namespace otel
```

### k8s

#### Port-forward Grafana accessible from other PC
```
kubectl port-forward --address 0.0.0.0 services/my-otel-demo-grafana 3000:80
```
