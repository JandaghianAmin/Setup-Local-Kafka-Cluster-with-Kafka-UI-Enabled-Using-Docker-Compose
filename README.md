# Setup-Local-Kafka-Cluster-with-Kafka-UI-Enabled-Using-Docker-Compose

Introduction

This tutorial will guide you through setting up a Kafka cluster with three brokers and a Kafka UI for easy monitoring, all using Docker Desktop. The Docker Compose file provided includes configurations for Kafka, Zookeeper, and Kafka UI.

Prerequisites

Docker installed on your machine.
Basic understanding of Docker and Docker Compose.
Create Docker Compose File Create a docker-compose.yml file in a directory of your choice. This file will define all the services required for Kafka and Zookeeper. Paste the following configuration into your file:

```
services:
  zookeeper:
    image: bitnami/zookeeper:3.9.1
    # to survive the container restart
    tmpfs: "/zktmp"
    environment:
      ALLOW_ANONYMOUS_LOGIN: 'yes'
    ports:
      - "2181:2181"

  kafka1:
    image: bitnami/kafka:3.7.0
    depends_on:
      - zookeeper
    environment:
      KAFKA_BROKER_ID: 1
      KAFKA_CFG_ZOOKEEPER_CONNECT: zookeeper:2181
      KAFKA_CFG_LISTENERS: INTERNAL://:9092,EXTERNAL://0.0.0.0:29092
      KAFKA_CFG_ADVERTISED_LISTENERS: INTERNAL://kafka1:9092,EXTERNAL://localhost:29092
      KAFKA_CFG_LISTENER_SECURITY_PROTOCOL_MAP: INTERNAL:PLAINTEXT,EXTERNAL:PLAINTEXT
      KAFKA_CFG_INTER_BROKER_LISTENER_NAME: INTERNAL
      # optional - enable topic auto create
      KAFKA_CFG_AUTO_CREATE_TOPICS_ENABLE: 'true'
      ALLOW_PLAINTEXT_LISTENER: 'yes'
    ports:
      - "9092:9092"
      - "29092:29092"
    volumes:
      - kafka_data1:/bitnami/kafka

  kafka2:
    image: bitnami/kafka:3.7.0
    depends_on:
      - zookeeper
    environment:
      KAFKA_BROKER_ID: 2
      KAFKA_CFG_ZOOKEEPER_CONNECT: zookeeper:2181
      KAFKA_CFG_LISTENERS: INTERNAL://:9093,EXTERNAL://0.0.0.0:29093
      KAFKA_CFG_ADVERTISED_LISTENERS: INTERNAL://kafka2:9093,EXTERNAL://localhost:29093
      KAFKA_CFG_LISTENER_SECURITY_PROTOCOL_MAP: INTERNAL:PLAINTEXT,EXTERNAL:PLAINTEXT
      KAFKA_CFG_INTER_BROKER_LISTENER_NAME: INTERNAL
      # optional - enable topic auto create
      KAFKA_CFG_AUTO_CREATE_TOPICS_ENABLE: 'true'
      ALLOW_PLAINTEXT_LISTENER: 'yes'
    ports:
      - "9093:9093"
      - "29093:29093"
    volumes:
      - kafka_data2:/bitnami/kafka

  kafka3:
    image: bitnami/kafka:3.7.0
    depends_on:
      - zookeeper
    environment:
      KAFKA_BROKER_ID: 3
      KAFKA_CFG_ZOOKEEPER_CONNECT: zookeeper:2181
      KAFKA_CFG_LISTENERS: INTERNAL://:9094,EXTERNAL://0.0.0.0:29094
      KAFKA_CFG_ADVERTISED_LISTENERS: INTERNAL://kafka3:9094,EXTERNAL://localhost:29094
      KAFKA_CFG_LISTENER_SECURITY_PROTOCOL_MAP: INTERNAL:PLAINTEXT,EXTERNAL:PLAINTEXT
      KAFKA_CFG_INTER_BROKER_LISTENER_NAME: INTERNAL
      # optional - enable topic auto create
      KAFKA_CFG_AUTO_CREATE_TOPICS_ENABLE: 'true'
      ALLOW_PLAINTEXT_LISTENER: 'yes'
    ports:
      - "9094:9094"
      - "29094:29094"
    volumes:
      - kafka_data3:/bitnami/kafka
      
  kafka-ui:
    image: provectuslabs/kafka-ui:latest
    depends_on:
      - kafka1
      - kafka2
      - kafka3
    ports:
      - "8080:8080"
    environment:
      KAFKA_CLUSTERS_0_NAME: local
      KAFKA_CLUSTERS_0_BOOTSTRAPSERVERS: kafka1:9092,kafka2:9093,kafka3:9094
      KAFKA_CLUSTERS_0_ZOOKEEPER: zookeeper:2181

volumes:
  kafka_data1:
    driver: local
  kafka_data2:
    driver: local
  kafka_data3:
    driver: local
```

2. Launch the Services Open your terminal, navigate to the directory where your docker-compose.yml file is located, and run the following command to start all services:

```
docker-compose up -d
```

This command will download the necessary Docker images, create the defined services, and start them in detached mode.

3. Verify the Setup To ensure everything is running as expected, you can check the status of the containers using:

```
docker-compose ps
```

4. Access Kafka UI Once all services are up, you can access Kafka UI by navigating to  ``` http://localhost:8080 ``` in your web browser. This UI will allow you to manage and monitor your Kafka cluster effectively.

Conclusion

You now have a Kafka cluster running with three brokers and a Zookeeper instance, all managed through Docker. Kafka UI provides a user-friendly interface to interact with your cluster. This setup is ideal for development and testing environments.
