# RabbitMQ cluster with Docker Compose

Creates a 3 node RabbitMQ cluster with a HAProxy acting as a load balancer.

You need to build the HAProxy image first, just run:
```sh
$ docker build -t haproxy-rabbitmq-cluster:1.7 .
```

Generate the self signed certificates
```sh
$ generate-ssl-key.sh
```

Now run the docker compose file:
```sh
$ docker-compose up -d
```

check if the containers are running:
```sh
$ docker ps
```

Create the cluster by running:
```sh
$ docker exec -ti rabbitmq-node-2 bash -c "rabbitmqctl stop_app"
$ docker exec -ti rabbitmq-node-2 bash -c "rabbitmqctl join_cluster rabbit@rabbitmq-node-1"
$ docker exec -ti rabbitmq-node-2 bash -c "rabbitmqctl start_app"

$ docker exec -ti rabbitmq-node-3 bash -c "rabbitmqctl stop_app"
$ docker exec -ti rabbitmq-node-3 bash -c "rabbitmqctl join_cluster rabbit@rabbitmq-node-1"
$ docker exec -ti rabbitmq-node-3 bash -c "rabbitmqctl start_app"
```

You can run the below script instead
```sh
$ up-cluster.sh
```

Check the cluster status:
```sh
$ docker exec -ti rabbitmq-node-1 bash -c "rabbitmqctl cluster_status"
```

Access HAProxy statistics report at `http://localhost:1936/haproxy?stats` with the credential `haproxy:haproxy`, and the RabbitMQ console at `http://localhost:15672/` with the credential `admin:Admin123`.

To verify that rmq client can send message run the golang program `test-go.go`.
```sh
go build test-go.go
./test-go
```



