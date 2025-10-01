# Activité Pratique N°1 - Event Driven Architecture avec KAFKA

Ce projet illustre l'architecture orientée événements avec Apache Kafka et Spring Cloud Stream. Il inclut la production, la consommation, le traitement analytique en temps réel et la visualisation des événements de pages web.

## Sommaire
- [Présentation](#présentation)
- [Prérequis](#prérequis)
- [Démarrage de l'infrastructure Kafka avec Docker](#démarrage-de-linfrastructure-kafka-avec-docker)
- [Structure du projet](#structure-du-projet)
- [Services Implémentés](#services-implémentés)
- [Configuration](#configuration)
- [Tester avec les outils Kafka](#tester-avec-les-outils-kafka)
- [Dashboard Web](#dashboard-web)
- [Technologies utilisées](#technologies-utilisées)

## Présentation

Ce projet met en œuvre :
- Un service Producer Kafka via un contrôleur REST
- Un service Supplier Kafka (génération automatique d'événements)
- Un service Consumer Kafka
- Un service d'Analytics temps réel avec Kafka Streams
- Une application web affichant les résultats analytiques en temps réel

## Prérequis
- Docker & Docker Compose
- Java 17+
- Maven
- Navigateur web

## Démarrage de l'infrastructure Kafka avec Docker

1. Créez le fichier `docker-compose.yml` (déjà présent dans le projet)
2. Démarrez les conteneurs :

```bash
# Dans le dossier du projet
docker-compose up -d
```

Cela lance :
- Zookeeper (port 2181)
- Kafka Broker (port 9092)

## Structure du projet

- `PageEvent`: Classe d'événement
- `PageEventController`: Endpoints REST et accès analytics
- `PageEventHandler`: Logique de stream processing
- `compose.yml`: Infrastructure Kafka
- `application.properties`: Configuration Spring Cloud Stream
- `static/index.html`: Dashboard web

## Services Implémentés

- **Producer REST** : `/publish?name={pageName}&topic={topicName}`
- **Supplier** : Génération automatique d'événements toutes les 200ms
- **Consumer** : Affiche les événements reçus dans la console
- **Analytics** : Traitement temps réel, filtrage, groupement par page, fenêtrage 5s, stockage dans un state store
- **Web UI** : Visualisation en temps réel via SSE et SmoothieCharts

## Configuration

Extrait de `application.properties` :
```properties
server.port=8080
spring.cloud.stream.bindings.pageEventConsumer-in-0.destination=T2
spring.cloud.stream.bindings.pageEventSupplier-out-0.destination=T3
spring.cloud.stream.bindings.pageEventSupplier-out-0.producer.poller.fixed-delay=200
spring.cloud.stream.bindings.kStreamFunction-in-0.destination=T3
spring.cloud.stream.bindings.kStreamFunction-out-0.destination=T4
spring.cloud.function.definition=pageEventConsumer;pageEventSupplier;kStreamFunction
spring.cloud.stream.kafka.streams.binder.configuration.commit.interval.ms=1000
```

## Tester avec les outils Kafka

Producteur :
```bash
docker exec -it kafka-broker kafka-console-producer --bootstrap-server localhost:9092 --topic T2
```
Consommateur :
```bash
docker exec -it kafka-broker kafka-console-consumer --bootstrap-server localhost:9092 --topic T2
```

## Dashboard Web

Accédez à l'interface :
```
http://localhost:8080
```
- Visualisation en temps réel des analytics
- Graphique pour les pages P1 et P2
- Mise à jour automatique chaque seconde

## Technologies utilisées
- Spring Boot
- Spring Cloud Stream
- Apache Kafka & Kafka Streams
- Docker & Docker Compose
- SmoothieCharts.js
