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

 export POD_NAME=$(kubectl get pods --namespace default -l "app.kubernetes.io/name=zookeeper,app.kubernetes.io/instance=zookeeper,app.kubernetes.io/component=zookeeper" -o jsonpath="{.items[0].metadata.name}")
    kubectl exec -it $POD_NAME -- zkCli.sh

kubectl port-forward --namespace default svc/zookeeper 2181:2181 &
    zkCli.sh 127.0.0.1:2181


kubectl run kafka-client --restart='Never' --image docker.io/bitnami/kafka:2.6.0-debian-10-r57 --namespace default --command -- sleep infinity
    kubectl exec --tty -i kafka-client --namespace default -- bash

    PRODUCER:
        kafka-console-producer.sh \
            
            --broker-list kafka-0.kafka-headless.default.svc.cluster.local:9092,kafka-1.kafka-headless.default.svc.cluster.local:9092,kafka-2.kafka-headless.default.svc.cluster.local:9092 \
            --topic test

    CONSUMER:
        kafka-console-consumer.sh \
            
            --bootstrap-server kafka.default.svc.cluster.local:9092 \
            --topic test \
            --from-beginning


helm install kafka kafka --set external.externalZookeeper.servers =zookeeper.default.svc.cluster.local


demo chagnes for assignment6 
