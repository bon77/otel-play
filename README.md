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
helm repo add grafana https://grafana.github.io/helm-charts

```
#### Install
##### OpenTelemetry
```
kubectl create namespace otel
helm install otel --namespace otel -f collector-values.yaml open-telemetry/opentelemetry-collector
```

##### Mimir
```
kubectl create namespace mimir
helm -n mimir install mimir grafana/mimir-distributed
```

##### Grafana
```
helm -n grafana install grafana grafana/grafana
```

1. Get your 'admin' user password by running:
```
   kubectl get secret --namespace grafana grafana -o jsonpath="{.data.admin-password}" | base64 --decode ; echo
```

2. The Grafana server can be accessed via port 80 on the following DNS name from within your cluster:

   grafana.grafana.svc.cluster.local

   Get the Grafana URL to visit by running these commands in the same shell:
```
export POD_NAME=$(kubectl get pods --namespace grafana -l "app.kubernetes.io/name=grafana,app.kubernetes.io/instance=grafana" -o jsonpath="{.items[0].metadata.name}")
kubectl --namespace grafana port-forward $POD_NAME 3000
```

3. Login with the password from step 1 and the username: admin

4. Add the `Mimir` / `Prometheus` Datasource
Connections -> Data sources -> Add

Prometheus server URL: `http://mimir-nginx.mimir/prometheus`
Add the custom HTTP header: `X-Scope-OrgID` with value `BANANA`

#### Cleanup
```
helm list --all-namespaces
helm delete otel --namespace otel
kind delete cluster -n unkind
```

### k8s

#### Port-forward Grafana accessible from other PC
```
kubectl --namespace grafana port-forward --address 0.0.0.0 services/grafana 3000:80
```
