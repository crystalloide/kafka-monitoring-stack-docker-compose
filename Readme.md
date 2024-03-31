# Kafka Monitoring Stack for Docker Compose (Prometheus / Grafana)

# Démonstration de l'utilisation de Prometheus et Grafana pour le monitoring d'un cluster Apache Kafka :



[![Open in Gitpod](https://gitpod.io/button/open-in-gitpod.svg)](https://gitpod.io/#https://github.com/crystalloide/kafka-monitoring-stack-docker-compose)

#### Rappel :
[# Pour afficher les workspaces déjà instanciés si besoin de faire du ménage :](https://gitpod.io/workspaces)

#### Affichage du répertoire courant dans Gitpod : 

    pwd



# Stack :

    
    * Kafka (Confluent)
    * Zookeeper
    * Kafka Schema Registry (Confluent)
    * KSQLDB
    * Kafka Connect
    * Prometheus
    * Grafana
    * AKHQ (https://akhq.io/)
    * ZooNavigator (https://zoonavigator.elkozmon.com/en/stable/)

    == Usage

    [source, bash]
    ----
    $ ./up -h

    --------------------------------------------------------------------------------
          ____        __           _____ __                            _
         / __ \____ _/ /_____ _   / ___// /_________  ____ _____ ___  (_)___  ____ _
        / / / / __ `/ __/ __ `/   \__ \/ __/ ___/ _ \/ __ `/ __ `__ \/ / __ \/ __ `/
       / /_/ / /_/ / /_/ /_/ /   ___/ / /_/ /  /  __/ /_/ / / / / / / / / / / /_/ /
      /_____/\__,_/\__/\__,_/   /____/\__/_/   \___/\__,_/_/ /_/ /_/_/_/ /_/\__, /
                                                                            /____/
    
     Powered by Apache Kafka!
    
    --------------------------------------------------------------------------------
    Usage: ./up [options]
    	 -n <[NAME]>      	: Name of the stack to deploy (required)
    	 -s <[SERVICE]>   	: Service names to deploy (run all services if empty)
    	 -h               	: Print this Help.
    
    The table below lists the available docker-compose stacks:

    NAME (./up -n <NAME>)              |	FILE (docker compose -f <FILE> up -d)
    ===================================+==================================================
    zk-kafka-multiple-nodes-sasl       |	zk-kafka-multiple-nodes-sasl-stack.yml
    zk-kafka-multiple-nodes            |	zk-kafka-multiple-nodes-stack.yml
    zk-kafka-single-node-full          |	zk-kafka-single-node-full-stack.yml
    zk-kafka-single-node-sasl          |	zk-kafka-single-node-sasl-stack.yml
    zk-kafka-single-node               |	zk-kafka-single-node-stack.yml
    zkless-kafka-multiple-nodes        |	zkless-kafka-multiple-nodes-stack.yml
    ----
    '''
    
    === Examples

    **To deploy a Kafka Cluster (Kraft)**
    [source, bash]
    ----
    $ ./up -n zkless-kafka-multiple-nodes
    ----
    
    == Getting Started

## **1. Clone the Kafka Monitoring Suite repository.**

    git clone https://github.com/streamthoughts/kafka-monitoring-stack-docker-compose.git
    
    cd kafka-monitoring-stack-docker-compose


## **2. Start Confluent/Kafka cluster.**

Deploy one of the provided docker-compose stack:

_Note: Depending on your network speed, this may take few minutes to download all images._

=== Single Zookeeper/Kafka with Prometheus/Grafana

Start/Stop with:

    ./zk-kafka-single-node-stack-start.sh
    ./zk-kafka-single-node-stack-stop.sh

### or directly
    docker-compose -f zk-kafka-single-node-stack.yml up -d
    docker-compose -f zk-kafka-single-node-stack.yml down


## Single Zookeeper / Multiple Kafka with Prometheus/Grafana


### Lancement :

    docker-compose -f zk-kafka-multiple-nodes-stack.yml up -d
    
### Arrêt :    

    docker-compose -f zk-kafka-multiple-nodes-stack.yml down


## Single Zookeeper/Kafka with Prometheus/Grafana (SASL/PLAINTEXT)

----
    ./zk-kafka-single-node-sasl-stack-start.sh
    
    ./zk-kafka-single-node-sasl-stack-stop.sh
----

## Single Zookeeper / Multiple Kafka with Prometheus/Grafana (SASL/PLAINTEXT)

[source,bash]
----
$ docker-compose -f  zk-kafka-multiple-nodes-sasl-stack.yml up -d
$ docker-compose -f  zk-kafka-multiple-nodes-sasl-stack.yml down
----

## Full Stack (single node Kafka Cluster)

Start/Stop with:

[source,bash]
----
$ ./full-single-node-stack-start.sh
$ ./full-single-node-stack-stop.sh

# or directly
$ docker-compose -f  zk-kafka-single-node-full-stack.yml up -d
$ docker-compose -f  zk-kafka-single-node-full-stack.yml down
----

**3. Create Topic.**

Create `demo-topic` with 6 partitions and 3 replicas.

[source,bash]
----
$ docker exec -it kafka101 \
kafka-topics \
--create \
--partitions 6 \
--replication-factor 3 \
--topic demo-topic \
--bootstrap-server kafka101:29092
----

**4. Produce messages.**

Open a new terminal window, generate some message to simulate producer load.

[source,bash]
----
$ docker exec -it kafka101 \
kafka-producer-perf-test \
--throughput 500 \
--num-records 100000000 \
--topic demo-topic \
--record-size 100 \
--producer-props bootstrap.servers=kafka101:29092
----

**5. Consume messages.**

Open a new terminal window, generate some message to simulate consumer load.

[source,bash]
----
$ docker exec -it kafka101 \
kafka-consumer-perf-test \
--messages 100000000 \
--timeout 1000000 \
--topic demo-topic \
--reporting-interval 1000 \
--show-detailed-stats \
--bootstrap-server kafka101:29092
----

**6. Open Grafana.**

Open your favorite web browser and open one of the provided Grafana dashboards :

* Kafka Cluster / Global Health Check

image:./assets/kafka-cluster-healthcheck.png[kafka-cluster-healthcheck]

* Kafka Cluster / Performance

image:./assets/kafka-cluster-performance.png[kafka-cluster-performance]

* Kafka Cluster / Zookeeper Connections
* Kafka Cluster / JVM & OS
* Kafka Cluster / Hard disk usage
* Kafka Cluster / Topic Logs

image:./assets/kafka-cluster-logs.png[kafka-cluster-log]

=== Accessing Grafana Web UI

Grafana is accessible at the address : http://localhost:3000

Security are :

* user : `admin`
* password : `kafka`

=== Accessing Prometheus Web UI

Prometheus is accessible at the address : http://localhost:9090

== 💡 Contributions

Any feedback, bug reports and PRs are greatly appreciated!

== 🙏 Show your support

Please ⭐ this repository to support us!

== Licence

This code base is available under the Apache License, version 2.
