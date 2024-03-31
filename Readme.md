# Kafka Monitoring Stack for Docker Compose (Prometheus / Grafana)

# Démonstration de l'utilisation de Prometheus et Grafana pour le monitoring d'un cluster Apache Kafka :



[![Open in Gitpod](https://gitpod.io/button/open-in-gitpod.svg)](https://gitpod.io/#https://github.com/crystalloide/kafka-monitoring-stack-docker-compose)

#### Rappel :
[# Pour afficher les workspaces déjà instanciés si besoin de faire du ménage :](https://gitpod.io/workspaces)


#### Si on veut faire ce TP (en dehors de Gitpod, sur une VM à soi par exemple) : 
## **1. Clonage du repository "Kafka Monitoring" :**

    git clone https://github.com/streamthoughts/kafka-monitoring-stack-docker-compose.git
    
    cd kafka-monitoring-stack-docker-compose
  

#### Affichage du répertoire courant : 

    pwd


# Stack complète disponible :
    
    * Kafka (Confluent)
    * Zookeeper
    * Kafka Schema Registry (Confluent)
    * KSQLDB
    * Kafka Connect
    * Prometheus
    * Grafana
    * AKHQ (https://akhq.io/)
    * ZooNavigator (https://zoonavigator.elkozmon.com/en/stable/)

### Utilisation : 

    ./up -h
    
### 
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
    
### Exemple : 

####   **Pour déployer un cluster Kafka (Kraft)**
```
   ./up -n zkless-kafka-multiple-nodes
```  


## **2. Démarrage du cluster souhaité (Confluent/Kafka) :**

### Exemple d'utilisation :

    ./up -n kraft-single-node-full 

#### Et le déploiement commence :    

    ✔ Network kafka-platform                                           Created                                                                                                                                                                           0.1s 
    ✔ Container kafka101                                               Started                                                                                                                                                                           0.9s 
    ✔ Container prometheus                                             Started                                                                                                                                                                           0.9s 
    ✔ Container grafana                                                Started                                                                                                                                                                           0.8s 
    ✔ Container jmx-kafka101                                           Started                                                                                                                                                                           0.9s 
    ✔ Container kafka-schema-registry                                  Started                                                                                                                                                                           0.8s 
    ✔ Container akhq                                                   Started                                                                                                                                                                           1.3s 
    ✔ Container kafka-monitoring-stack-docker-compose-kafka-connect-1  Started                                                                                                                                                                           1.4s 

--------------------------------------------------------------------------------
    Grafana (Login : admin / Password : kafka) :  http://localhost:3000
    Prometheus :                                  http://localhost:9090
--------------------------------------------------------------------------------

 ### Configuration du client à indiquer pour se connecter au cluster :

	bootstrap.servers=localhost:9092



## Déploiement au choix de l'une des configurations docker-compose propsoées :

_Note: Depending on your network speed, this may take few minutes to download all images._

=== Single Zookeeper/Kafka with Prometheus/Grafana

### Lancement :

    ./zk-kafka-single-node-stack-start.sh
    
### Arrêt :  

    ./zk-kafka-single-node-stack-stop.sh

### Ou sinon directement : 

    docker compose -f zk-kafka-single-node-stack.yml up -d
    
  puis  
  
    docker compose -f zk-kafka-single-node-stack.yml down


## Single Zookeeper / Multiple Kafka with Prometheus/Grafana


### Lancement :

    docker-compose -f zk-kafka-multiple-nodes-stack.yml up -d
    
### Arrêt :    

    docker-compose -f zk-kafka-multiple-nodes-stack.yml down


## Single Zookeeper/Kafka with Prometheus/Grafana (SASL/PLAINTEXT)

### Pour lancer : 

    ./zk-kafka-single-node-sasl-stack-start.sh
    
### Puis, pour arrêter :    

    ./zk-kafka-single-node-sasl-stack-stop.sh


## Single Zookeeper / Multiple Kafka with Prometheus/Grafana (SASL/PLAINTEXT)

### Pour lancer :  
 
    docker-compose -f  zk-kafka-multiple-nodes-sasl-stack.yml up -d
 
### Puis, pour arrêter :  
 
    docker-compose -f  zk-kafka-multiple-nodes-sasl-stack.yml down


## Full Stack (single node Kafka Cluster)

### Pour lancer :  

    ./full-single-node-stack-start.sh
    
### Puis, pour arrêter :  
    
    ./full-single-node-stack-stop.sh

### Ou sinon directement :

### Pour lancer :  
    docker-compose -f  zk-kafka-single-node-full-stack.yml up -d
    
### Puis, pour arrêter :  
    docker-compose -f  zk-kafka-single-node-full-stack.yml down


**3. Create Topic.**

Create `demo-topic` with 6 partitions and 3 replicas.

```
docker exec -it kafka101 \
kafka-topics \
--create \
--partitions 6 \
--replication-factor 3 \
--topic demo-topic \
--bootstrap-server kafka101:29092
```

**4. Produce messages.**

Open a new terminal window, generate some message to simulate producer load.

```
docker exec -it kafka101 \
kafka-producer-perf-test \
--throughput 500 \
--num-records 100000000 \
--topic demo-topic \
--record-size 100 \
--producer-props bootstrap.servers=kafka101:29092
```

**5. Consume messages.**

Open a new terminal window, generate some message to simulate consumer load.

```
docker exec -it kafka101 \
kafka-consumer-perf-test \
--messages 100000000 \
--timeout 1000000 \
--topic demo-topic \
--reporting-interval 1000 \
--show-detailed-stats \
--bootstrap-server kafka101:29092
```

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
