# Description du Projet 03 JENKINS
   Lien vers le **Projet 01 DOCKER** de la formation Bootcamp DevOps : 
   [here](https://github.com/omarvm001/Devops_projet_01_Docker "here")
   Lien vers le **Projet 02 GITLAB-CI** de la formation Bootcamp DevOps : 
   [here](https://github.com/omarvm001/Devops-projet-02-GITLAB "here")
   
   Ce rapport détaille les étapes du pipeline décrites dans le fichier **Jenkinsfile**, **Dockerfile** ainsi que les bénéfices associés à l'automatisation complète du processus de développement, test et déploiement d'une application via un pipeline CI/CD (Intégration Continue / Livraison Continue) sur **Jenkins**, en utilisant des conteneurs **Docker** et des environnements cloud comme **Heroku**.

![project](https://github.com/omarvm001/Devops-projet-02-GITLAB/blob/main/images/pipeline-ci-cd.jpeg)
![project](https://github.com/omarvm001/Devops-projet-03-JENKINS/blob/master/images/img_jenkins.png)

# Conteneurisation & Déploiement CI/CD via Jenkins d'une Application Web Statique

Dans ce projet, une application web statique est conteneurisée et un pipeline CI/CD complet est mis en place via **Jenkins** pour automatiser les étapes de build, test et déploiement sur **Heroku**. 

Le `Dockerfile` décrit la construction de l'image en clonant le code source, puis en l’intégrant dans un serveur **Nginx** minimaliste pour servir l'application. Le `Jenkinsfile` structure le pipeline en plusieurs étapes, incluant la construction de l'image Docker, des tests d'acceptation, la publication de l'image sur **Docker Hub**, ainsi que le déploiement automatique sur les environnements Heroku (staging et production).

Le pipeline utilise des services comme `docker:dind` pour la gestion des conteneurs Docker et des configurations d'environnement pour automatiser le déploiement et les tests dans différents environnements. Chaque étape est orchestrée pour assurer une livraison continue, en passant par des phases de tests automatisés avant chaque mise en production.


# CI/CD Pipeline avec Jenkins pour une Application Web Statique

Ce projet met en place un pipeline CI/CD via Jenkins pour automatiser le processus de **build**, **test** et **déploiement** d'une application web statique sur Docker et Heroku. L'application est conteneurisée avec Docker et est servie par Nginx. Le pipeline gère automatiquement les déploiements dans les environnements staging et production sur Heroku.

## Table des Matières
1. [Prérequis](#prérequis)
2. [Technologies Utilisées](#technologies-utilisées)
3. [Pipeline CI/CD - Jenkinsfile](#pipeline-cicd---jenkinsfile)
   - [Build Image](#1-build-image)
   - [Run Container Based on Built Image](#2-run-container-based-on-built-image)
   - [Test Image](#3-test-image)
   - [Clean Container](#4-clean-container)
   - [Login and Push Image on Docker Hub](#5-login-and-push-image-on-docker-hub)
   - [Push Image in Staging and Deploy It](#6-push-image-in-staging-and-deploy-it)
   - [Push Image in Production and Deploy It](#7-push-image-in-production-and-deploy-it)
4. [Notifications](#notifications)

## Prérequis

- [Docker](https://www.docker.com/) pour la conteneurisation.
- [Jenkins](https://www.jenkins.io/) pour orchestrer le pipeline CI/CD.
- [Heroku CLI](https://devcenter.heroku.com/articles/heroku-cli) pour le déploiement sur Heroku.
- Compte Docker Hub pour stocker les images Docker.

## Technologies Utilisées

Ce projet fait appel à plusieurs technologies pour assurer une intégration continue fluide, un déploiement automatisé et un hébergement fiable de l'application web statique.

### 1. Docker

[Docker](https://www.docker.com/) a été utilisé pour **conteneuriser** l'application. L'application est isolée dans un conteneur léger avec toutes les dépendances nécessaires. Cela garantit :
- **Portabilité** : Fonctionne de manière cohérente dans différents environnements.
- **Isolation** : Les dépendances sont encapsulées dans le conteneur.
- **Gestion simplifiée des versions** : Chaque commit correspond à une image Docker identifiable par un tag.

### 2. Jenkins

[Jenkins](https://www.jenkins.io/) est utilisé pour automatiser les processus de **build**, **test** et **déploiement**. Le pipeline CI/CD inclut les étapes suivantes :
- **Build** : Construction de l'image Docker à partir du Dockerfile.
- **Tests d'acceptation** : Vérification du contenu de l'application pour valider le déploiement.
- **Publication de l'image** : Poussée de l'image Docker sur Docker Hub.
- **Déploiement** : Automatisation du déploiement sur Heroku dans les environnements staging et production.

### 3. Heroku

[Heroku](https://www.heroku.com/) est utilisé pour **héberger l'application** dans deux environnements :
- **Staging** : Déploiement de l'application pour des tests pré-production.
- **Production** : L'environnement final accessible aux utilisateurs.


---

## Pipeline CI/CD - Jenkinsfile

Le fichier Jenkinsfile décrit chaque étape du pipeline. Voici un aperçu détaillé :

### 1. Build Image

   - **Résumé** : Cette étape construit une image Docker en utilisant le Dockerfile. L’image est taguée avec un identifiant unique.
   - **Objectif** : Préparer une image conteneurisée de l'application pour la phase de test et de déploiement.

   ```groovy
   stage('Build image') {
       agent any
       steps {
          script {
            sh 'docker build -t ${DOCKERHUB_ID}/$IMAGE_NAME:$IMAGE_TAG .'
          }
       }
   }
```

### 2. Run Container Based on Built Image

### 3. Test Image

### 4. Clean Container

### 5. Login and Push Image on Docker Hub

### 6. Push Image in Staging and Deploy It

### 7. Push Image in Production and Deploy It


