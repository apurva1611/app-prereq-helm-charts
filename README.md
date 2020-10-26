# app-prereq-helm-charts
setup the pre-requisites on the Kubernetes cluster before application is installed/setup.
Steps:
1. Go to APP-PREREQ-HELM-CHARTS directory:
2. To create zookeeper chart run
helm install zookeeper zookeeper
Get a zookeeeper service name
3. to run kafka cluster:
helm install kafka kafka (In values.yml in externalZookeeper in servers put zookeeeper service name and put it to run kakfa with the above created zookeeper.)

// Point to note this kakfa does not create topic. The charts just install kakfa and zookeeper on cluster.

#zookepernew-zookeeper.default.svc.cluster.local

export POD_NAME=$(kubectl get pods --namespace default -l "app.kubernetes.io/name=kafka,app.kubernetes.io/instance=kafka,app.kubernetes.io/component=kafka" -o jsonpath="{.items[0].metadata.name}")

kubectl --namespace default eexport POD_NAME=$(kubectl get pods --namespace default -l "app.kubernetes.io/name=kafka,app.kubernetes.io/instance=kafka,app.kubernetes.io/component=kafka" -o jsonpath="{.items[0].metadata.name}")xec -it $POD_NAME -- kafka-topics.sh --create --zookeeper zookepernew-zookeeper.default.svc.cluster.local:2181 --replication-factor 1 --partitions 1 --topic mytopic
