# Log-Monitoring
#Build the Docker image from the compose


docker-compose -f docker-compose-dist-logging.yaml build

#Run the Docker image that will set up all the containers

docker-compose -f docker-compose-dist-logging.yaml up

#Exec into the kafka_connect container and use the REST api to add both Elasticsearch and S3 connectors

docker-compose -f docker-compose-dist-logging.yaml exec kafka_connect bash

curl -X POST -H "Content-Type: application/json" --data @/opt/connector_conf/connector_elasticsearch.json http://localhost:8083/connectors

curl -X POST -H "Content-Type: application/json" --data @/opt/connector_conf/connector_s3.json http://localhost:8083/connectors

#####################################################################################################################################
Brief::
Apache Kafka almost needs no introduction. It’s a popular distributed pub-sub messaging platform that offers persistent store and high scalability. Aggregating all our Docker container logs on Kafka allows us to handle high message throughput and from there route them to any number of downstream systems using Kafka Connect.

Kafka Connect is a collective name for a set of connector that connects Kafka with external systems, e.g. JDBC databases, AWS S3, Google Cloud BigQuery, etc. Each connector can be installed separately on the Connect platform and users can interact with connectors via a REST interface on Kafka Connect. We will use the S3 and ElasticSearch connector in our log processing.

Elasticsearch is an popular open-source index and search software. For our purpose we will sink and index our logs into elasticsearch that can be analyzed in real time.

The overall architecture looks like this.

We create  Docker containers that runs an Apache HTTP server using an existing httpd image. It has a REST api that produces logs every time it receives a GET request. We also map host port 8080 to container port 80, where apache server runs on so we can curl the web app at http://localhost:8080/. It also links the fluentd container so that logs can be forwarded across containers. Lastly we set up a logging driver named fluentd with a tag.
