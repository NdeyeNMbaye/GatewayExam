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

### Connexion 

<img width="1917" height="964" alt="image" src="https://github.com/user-attachments/assets/29e16934-29a3-4d77-9966-788589456370" />

### reponse  avec le token genere a la connexion 

<img width="1904" height="969" alt="image" src="https://github.com/user-attachments/assets/52ded6d0-a1e5-4a24-8fff-f9fd1ca7d634" />

### liste des utilisateurs 

<img width="1909" height="973" alt="image" src="https://github.com/user-attachments/assets/48b65746-8bcd-4b7d-9a12-fd17874be918" />

### DIPLOMES 

### Liste des diplomes 

<img width="1919" height="981" alt="image" src="https://github.com/user-attachments/assets/f6dd0a1f-dd45-4cd4-8baf-add36d153654" />

### ajout d'un diplome 

<img width="1908" height="966" alt="image" src="https://github.com/user-attachments/assets/4127676b-4c61-4372-b05d-a8d2a838b43c" />

### REPONSE 

<img width="1917" height="975" alt="image" src="https://github.com/user-attachments/assets/3c7e9cc7-b4f6-4dd5-b87c-6307c729e875" />

<img width="1871" height="925" alt="image" src="https://github.com/user-attachments/assets/97a382a1-0f31-42c4-a6b7-37675d336ab7" />




### competences 

#### liste des competences 

<img width="1907" height="967" alt="image" src="https://github.com/user-attachments/assets/96da2a81-71b5-461a-ba1a-e205b66ae379" />


### Ajout de competences

<img width="1899" height="855" alt="image" src="https://github.com/user-attachments/assets/93eb69f1-f22e-4485-823d-dba6da57a634" />

### Competencies Service - Communication synchrone

(a COMPLETER )

###   Kafka - Communication asynchrone

### Docker Hub - Images poussées

<img width="1915" height="620" alt="image" src="https://github.com/user-attachments/assets/1fec9198-2379-47d3-871c-d8ffada5aa3e" />

## DEVOPS

CI/CD
Chaque microservice dispose d'un fichier .github/workflows/ci.yml qui :

Compile le projet avec Maven
Construit l'image Docker
Pousse l'image sur Docker Hub

### DIplome service 

<img width="1912" height="974" alt="image" src="https://github.com/user-attachments/assets/07295257-00bc-4746-89dd-a117bde4d81c" />


### competences service 

<img width="1911" height="960" alt="image" src="https://github.com/user-attachments/assets/174fa5e9-ddfa-422c-975f-d4a05712a0af" />

### User service 

<img width="1912" height="987" alt="image" src="https://github.com/user-attachments/assets/6ba97591-bdfb-4084-9267-fdde311a7557" />










