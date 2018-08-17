# Self Hosted

puppet-master can be self hosted using docker. Currently only example config for docker-compose is available, but providing an example helm chart is planned.

## docker-compose

There is an example [docker-compose.yml](https://github.com/Scalify/puppet-master/blob/master/examples/self_hosted/docker-compose.yml) available as a quickstart reference. To start a puppet-master instance locally using docker-compose execute the following:

```sh
mkdir puppet-master && cd puppet-master
wget https://raw.githubusercontent.com/Scalify/puppet-master/master/examples/self_hosted/docker-compose.yml
docker-compose up -d

```

To stop puppet-master just tear the docker-compose stack down:

```sh
docker-compose down
# optional: clean up data directory
# rm -rf ./data
```

## Used components / Services

### Gateway

*Source @ Github: [Scalify/puppet-master-gateway](https://github.com/Scalify/puppet-master-gateway) |
Docker Image: [scalify/puppet-master-gateway](https://hub.docker.com/r/scalify/puppet-master-gateway)*

The gateway service acts as a HTTP API. It stores the jobs at the CouchDB and coordinates the job executions by populating them to a RabbitMQ queue as well as consuming the job results from a RabbitMQ queue, persisting them back to CouchDB.

### Executor

*Source @ Github: [Scalify/puppet-master-executor](https://github.com/Scalify/puppet-master-executor) |
Docker Image: [scalify/puppet-master-executor](https://hub.docker.com/r/scalify/puppet-master-executor)*

The executor consumes jobs from a RabbitMQ queue, executes them and pushes the results back to another queue.


### Database (CouchDB)

*Docker Image: [couchdb:2.2](https://hub.docker.com/_/couchdb/)*

[CouchDB](http://couchdb.apache.org/) database used and accessed by the gateway, acting as the primary datastore of puppet-master.

### Queue (RabbitMQ)

*Docker Image: [rabbitmq:3.7.7-management-alpine](https://hub.docker.com/_/rabbitmq/)*

RabbitMQ AMQP Server used for the asynchroneus communication between the gateway and the executor(s).

[TBD]
