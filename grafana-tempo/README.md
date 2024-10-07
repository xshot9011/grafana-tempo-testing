# Grafana Tempp

```txt
helm repo add grafana https://grafana.github.io/helm-charts
helm repo update
helm upgrade --install grafana-tempo grafana/tempo -f value.yaml --namespace monitoring --create-namespace --version 1.10.3
```