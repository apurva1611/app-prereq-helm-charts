Elasticsearch Exporter
Prometheus exporter for various metrics about ElasticSearch, written in Go.

Learn more: https://github.com/justwatchcom/elasticsearch_exporter

This chart creates an Elasticsearch-Exporter deployment on a Kubernetes cluster using the Helm package manager.

Prerequisites
Kubernetes 1.10+
Get Repo Info
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
helm repo update
See helm repo for command documentation.

Install Chart
# Helm 3
$ helm install [RELEASE_NAME] prometheus-community/prometheus-elasticsearch-exporter

# Helm 2
$ helm install --name [RELEASE_NAME] prometheus-community/prometheus-elasticsearch-exporter
The command deploys Elasticsearch Exporter on the Kubernetes cluster using the default configuration.

See configuration below.

See helm install for command documentation.

Uninstall Chart
# Helm 3
$ helm uninstall [RELEASE_NAME]

# Helm 2
# helm delete --purge [RELEASE_NAME]
This removes all the Kubernetes components associated with the chart and deletes the release.

See helm uninstall for command documentation.

Upgrading Chart
# Helm 3 or 2
$ helm upgrade [RELEASE_NAME] [CHART] --install
See helm upgrade for command documentation.

To 4.0.0
While migrating the chart from stable/elasticsearch-exporter it was renamed to prometheus-elasticsearch-exporter. If you want to upgrade from a previous version and you need to keep the old resource names (Service, Deployment, etc) you can set fullnameOverride and nameOverride to do so.

The example below shows how those values should be set for a my-exporter release of the previous chart.

helm install my-exporter stable/elasticsearch-exporter
helm upgrade my-exporter . --set fullnameOverride=my-exporter-elasticsearch-exporter --set nameOverride=elasticsearch-exporter
