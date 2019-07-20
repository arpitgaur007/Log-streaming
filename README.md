# Log-Monitoring
#Build the Docker image from the compose
docker-compose -f docker-compose-dist-logging.yaml build
#Run the Docker image that will set up all the containers
docker-compose -f docker-compose-dist-logging.yaml up
#Exec into the kafka_connect container and use the REST api to add both Elasticsearch and S3 connectors
docker-compose -f docker-compose-dist-logging.yaml exec kafka_connect bash
curl -X POST -H "Content-Type: application/json" --data @/opt/connector_conf/connector_elasticsearch.json http://localhost:8083/connectors
curl -X POST -H "Content-Type: application/json" --data @/opt/connector_conf/connector_s3.json http://localhost:8083/connectors
