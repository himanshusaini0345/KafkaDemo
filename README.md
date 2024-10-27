# Kafka Docker Setup Guide

This guide explains how to set up a basic Kafka and Zookeeper environment using Docker. This configuration uses Confluent’s Docker images for Kafka and Zookeeper.

## Prerequisites

- Docker should be installed on your system.

## Steps

### 1. Pull Docker Images for Zookeeper and Kafka

First, pull the required Docker images for Zookeeper and Kafka.

```bash
docker pull confluentinc/cp-zookeeper
docker pull confluentinc/cp-kafka
```

### 2. Create a Docker Network

Create a custom Docker network for communication between Kafka and Zookeeper containers.

```bash
docker network create kafka
```

### 3. Start Zookeeper Container

Run the Zookeeper container and set the necessary environment variables for client port and tick time.

```bash
docker run -d --network=kafka --name=zookeeper \
    -e ZOOKEEPER_CLIENT_PORT=2181 \
    -e ZOOKEEPER_TICK_TIME=2000 \
    -p 2181:2181 \
    confluentinc/cp-zookeeper
```

**Explanation of Parameters:**
- `--network=kafka`: Connects the container to the kafka network.
- `--name=zookeeper`: Names the container "zookeeper".
- `-e ZOOKEEPER_CLIENT_PORT=2181`: Sets the Zookeeper client port to 2181.
- `-e ZOOKEEPER_TICK_TIME=2000`: Configures tick time in milliseconds.

### 4. Start Kafka Container

Run the Kafka container and configure it to connect to the Zookeeper instance started in the previous step.

```bash
docker run -d --network=kafka --name=kafka \
    -p 9092:9092 \
    -e KAFKA_ZOOKEEPER_CONNECT=zookeeper:2181 \
    -e KAFKA_ADVERTISED_LISTENERS=PLAINTEXT://localhost:9092 \
    confluentinc/cp-kafka
```

**Explanation of Parameters:**
- `--network=kafka`: Connects the container to the kafka network.
- `--name=kafka`: Names the container "kafka".
- `-p 9092:9092`: Maps Kafka’s default port 9092 on the host.
- `-e KAFKA_ZOOKEEPER_CONNECT=zookeeper:2181`: Configures Kafka to connect to Zookeeper on port 2181.
- `-e KAFKA_ADVERTISED_LISTENERS=PLAINTEXT://localhost:9092`: Sets the listener for clients to connect to Kafka.

### 5. Cleaning Up

When you're done, stop and remove the Kafka and Zookeeper containers, and delete the custom Docker network.

```bash
docker stop kafka zookeeper
docker rm kafka zookeeper
docker network rm kafka
```
