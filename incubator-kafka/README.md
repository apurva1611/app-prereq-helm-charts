Install:
```
$ helm repo add incubator http://storage.googleapis.com/kubernetes-charts-incubator
$ helm install kafka -f values.yml incubator/kafka
```

## Test
Test client:
```
$ kubectl apply -f test.yml -o yaml --dry-run=client
```

Listen messages:
```
$ kubectl -n default exec -ti testclient -- /usr/bin/kafka-console-consumer --bootstrap-server kafka:9092 --topic weather --from-beginning
$ kubectl -n default exec -ti testclient -- /usr/bin/kafka-console-consumer --bootstrap-server kafka:9092 --topic watch --from-beginning
```

Interactive message producer:
```
$ kubectl -n default exec -ti testclient -- /usr/bin/kafka-console-producer --broker-list kafka-headless:9092 --topic weather
$ kubectl -n default exec -ti testclient -- /usr/bin/kafka-console-producer --broker-list kafka-headless:9092 --topic watch
```

List kafka-topics on test:
```
$ kubectl -n default exec testclient -- /usr/bin/kafka-topics --zookeeper kafka-zookeeper:2181 --list
```

Cleanup:
```
$ helm delete kafka
```

## Cluster access
kafkaURL= kafka:9092


## run assingment a6
Bring up infrastructure
setup-k8s-cluster
setup-rds
setup-rds-vpcPeering

Go to app-prereq-helm-charts / incubator-kaka
$ helm repo add incubator http://storage.googleapis.com/kubernetes-charts-incubator
$ helm install kafka -f values.yml incubator/kafka
$ kubectl apply -f test.yml -o yaml


Prepare images for web app, poller, notifier

$ helm install webapp webapp  --set secret.regcred.dockerconfigjson='<dockerconfig>',configmap.rdsurl='<dbinstance-endpoint>',image.repository='<image-name>'

For dockerconfigjson 

Encode this block of code with encoded docker username password
{
    "auths": {
        "https://index.docker.io/v1/": {
            "auth": “encoded(username:password)”
        },
        "HttpHeaders": {
            "User-Agent": "Docker-Client/19.03.13 (darwin)"
        }
    }
}

consume 
$ kubectl -n default exec -ti testclient -- /usr/bin/kafka-console-consumer --bootstrap-server kafka:9092 --topic weather --from-beginning
$ kubectl -n default exec -ti testclient -- /usr/bin/kafka-console-consumer --bootstrap-server kafka:9092 --topic watch --from-beginning

produce
$ kubectl -n default exec -ti testclient -- /usr/bin/kafka-console-producer --broker-list kafka-headless:9092 --topic weather
$ kubectl -n default exec -ti testclient -- /usr/bin/kafka-console-producer --broker-list kafka-headless:9092 --topic watch


$ helm uninstall webapp
  
teardown-k8s-cluster
teardown-rds
teardown-rds-vpcPeering



Other kubectl command 

$ kubectl get all
$ kubectl get pods
$ kubectl describe pod podname
$ kubectl logs -p podname
$ kubectl get statefulset
$ kubectl get deployment
$ kubectl delete pod podname
$ kubectl delete sac servicename
