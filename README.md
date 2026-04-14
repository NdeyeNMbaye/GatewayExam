## Examen Java M2 - Architecture Microservices
#### Description
Projet de gestion académique des diplômés développé en architecture microservices avec Spring Boot. Il comprend trois microservices communicant de manière synchrone (REST) et asynchrone (Kafka), une Gateway centralisée, et un pipeline CI/CD avec Docker.

### Prérequis

Java 17
Maven 3.9
PostgreSQL
Redis
Apache Kafka
Docker & Docker Hub
Git
### Architecture
+------------------+
                    |  Gateway Service |
                    |   port: 8003     |
                    +------------------+
                           |
          +----------------+----------------+
          |                |                |
+-----------------+ +------------------+ +------------------+
|  User Service   | | Diplomas Service | |Competencies Svc  |
|  port: 8019     | |  port: 8082      | |  port: 8020      |
|  DB: user_db    | |  DB: diplomas_db | |  DB: compet_db   |
+-----------------+ +------------------+ +------------------+
                           |                |
                    Communication Synchrone (REST)
                            |               | 
                   Communication Asynchrone (Kafka)

### Microservices
1. User Service (port 8019)

Gestion des comptes utilisateurs
Authentification JWT (register/login)
Base de données : PostgreSQL

2. Diplomas Service (port 8082)

Gestion des diplômes des diplômés
Communication synchrone : expose /api/diplomes/by-email
Base de données : PostgreSQL

3. Competencies Service (port 8020)

Gestion et certification des compétences
Communication synchrone : vérifie l'existence du diplômé dans Diplomas Service avant création
Communication asynchrone : publie un événement Kafka GRADUATE_UPDATED après création
Base de données : PostgreSQL

4. Insertion Service (port 8084)

Suivi de l'insertion professionnelle
Communication asynchrone : consomme les événements Kafka GRADUATE_UPDATED
Base de données : PostgreSQL

5. Gateway Service (port 8003)

Point d'entrée unique pour tous les microservices
Routing vers les services
Swagger agrégé

### Communication

Synchrone (REST)
Competencies Service → Diplomas Service
Avant de créer une compétence, le service vérifie que le diplômé existe :
##### GET http://localhost:8082/api/diplomes/by-email?email={email}
### Asynchrone (Kafka)
Competencies Service → Topic GRADUATE_UPDATED → Insertion Service
Après création d'une compétence, un événement est publié sur Kafka et consommé par le service d'insertion.
### Lancement
1. Démarrer Kafka
bashdocker run -d --name kafka -p 9092:9092 apache/kafka:latest
2. Démarrer Redis
bashredis-server
### Documentation Swagger

<img width="1913" height="975" alt="image" src="https://github.com/user-attachments/assets/6e4ca61b-a7d3-40d0-8c86-fa498803842c" />

### Swagger par service

ServiceURLUser Servicehttp://localhost:8019/swagger-ui/index.html
Diplomas Servicehttp://localhost:8082/swagger-ui/index.html
Competencies Servicehttp://localhost:8020/swagger-ui/index.html
Gatewayhttp://localhost:8003/swagger-ui.html

### User Service - Register & Login
### -Inscription de nouveau user nomme examen@gmail.com

<img width="1912" height="934" alt="image" src="https://github.com/user-attachments/assets/6f4a4229-82c2-4dbf-9b9a-b7a13d7ee835" />
### Reponse 
<img width="1904" height="958" alt="image" src="https://github.com/user-attachments/assets/97bd1a74-db9b-47e9-88c2-f7fc7a83207b" />



