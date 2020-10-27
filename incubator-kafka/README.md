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
```

Interactive message producer:
```
$ kubectl -n default exec -ti testclient -- /usr/bin/kafka-console-producer --broker-list kafka-headless:9092 --topic weather
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