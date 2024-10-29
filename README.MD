# Description du Projet 03 JENKINS
   Lien vers le **Projet 01 DOCKER** de la **formation Bootcamp DevOps** : 
   [here](https://github.com/omarvm001/Devops_projet_01_Docker "here")

   Lien vers le **Projet 02 GITLAB-CI** de la **formation Bootcamp DevOps** : 
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
4. [Notifications Sur Slack](#notifications-sur-slack)
5. [Apreçu des Résultats](#apreçu-des-résultats)
6. [Conclusion](#conclusion)

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

   - **Résumé** : Lance un conteneur à partir de l’image construite. Si un conteneur avec le même nom existe déjà, il est supprimé.
   - **Objectif** : S'assurer que l’image fonctionne en tant que conteneur avant de passer aux tests.

   ```groovy
  stage('Run container based on built image') {
   agent any
   steps {
     script {
       sh '''
           echo "Cleaning existing container if exist"
           docker ps -a | grep -i $IMAGE_NAME && docker rm -f $IMAGE_NAME
           docker run --name $IMAGE_NAME -d -p $APP_EXPOSED_PORT:$APP_CONTAINER_PORT -e PORT=$APP_CONTAINER_PORT ${DOCKERHUB_ID}/$IMAGE_NAME:$IMAGE_TAG
           sleep 5
       '''
     }
   }
}
```

### 3. Test Image

- **Résumé** : Teste l’application en envoyant une requête HTTP au conteneur pour vérifier la présence de contenu attendu.
- **Objectif** : Valider que l'application dans le conteneur est servie correctement.

   ```groovy
      stage('Test image') {
          agent any
          steps {
             script {
               sh 'curl 172.17.0.1 | grep -i "Dimension"'
             }
          }
      }
   ```

### 4. Clean Container

- **Résumé** : Arrête et supprime le conteneur de test pour garder l'environnement propre.
- **Objectif** : Libérer des ressources après les tests.

   ```groovy
      stage('Clean container') {
         agent any
         steps {
            script {
              sh 'docker stop $IMAGE_NAME && docker rm $IMAGE_NAME'
            }
         }
      }
   ```

### 5. Login and Push Image on Docker Hub

- **Résumé** : Pousse l'image Docker sur Docker Hub pour permettre le déploiement dans les environnements staging et production.
- **Objectif** : Publier l'image pour un accès depuis des environnements distants.

   ```groovy
      stage ('Login and Push Image on Docker Hub') {
         agent any
         steps {
            script {
              sh '''
                  echo $DOCKERHUB_PASSWORD | docker login -u $DOCKERHUB_ID --password-stdin
                  docker push ${DOCKERHUB_ID}/$IMAGE_NAME:$IMAGE_TAG
              '''
            }
         }
      }
   ```

### 6. Push Image in Staging and Deploy It

- **Résumé** : Déploie l'image sur Heroku en mode staging. Si l'application de staging n'existe pas, elle est créée.
- **Objectif** : Tester l'application en environnement staging avant le déploiement en production.

   ```groovy
      stage('Push image in staging and deploy it') {
         when {
            expression { GIT_BRANCH == 'origin/master' }
         }
         agent {
            docker { image 'franela/dind' }
         }
         environment {
            HEROKU_API_KEY = credentials('heroku_api_key')
         }
         steps {
            script {
               sh '''
                   apk --no-cache add npm
         		    npm install -g heroku
         		    heroku container:login
         		
         		    # Check if the app exists, if not, create it
         		    if ! heroku apps | grep -q "$STAGING"; then
         		        heroku create $STAGING
         		    fi
         		
         		    heroku stack:set container -a $STAGING
         		    heroku container:push web -a $STAGING
         		    heroku container:release web -a $STAGING
               '''
            }
         }
      }
   ```

### 7. Push Image in Production and Deploy It

- **Résumé** : Déploie l'image sur Heroku en mode production. L’application production est créée si elle n'existe pas.
- **Objectif** : Déployer la version finale de l’application pour les utilisateurs.

   ```groovy
      stage('Push image in production and deploy it') {
         when {
             expression { GIT_BRANCH == 'origin/master' }
         }
         agent {
             docker { image 'franela/dind' }
         }
         environment {
             HEROKU_API_KEY = credentials('heroku_api_key')
         }
         steps {
            script {
              sh '''
                apk --no-cache add npm
      		    npm install -g heroku
      		    heroku container:login
      		
      		    # Check if the app exists, if not, create it
      		    if ! heroku apps | grep -q "$PRODUCTION"; then
      		        heroku create $PRODUCTION
      		    fi
      		
      		    heroku stack:set container -a $PRODUCTION
      		    heroku container:push web -a $PRODUCTION
      		    heroku container:release web -a $PRODUCTION
              '''
            }
         }
      }
   ```


## Notifications Sur Slack

À la fin de chaque pipeline :

   - En cas de succès, un message vert est envoyé sur Slack.

   - En cas d’échec, un message rouge est envoyé sur Slack.

## Apreçu des Résultats
Dans cette section, vous trouverez des captures d'écran illustrant le déploiement réussi de notre application.

- **Pipeline CI/CD JENKINS** : l'image ci-dessous illustre l'execution du pipeline CI/CD via Jenkins :

![project](https://github.com/omarvm001/Devops-projet-03-JENKINS/blob/master/images/jenkins_all_pipeline.png)

- **Docker Hub** : Image déployée et disponible dans le registre Docker Hub sous le nom du projet, permettant une réutilisation facile pour des déploiements futurs.

![project](https://github.com/omarvm001/Devops-projet-03-JENKINS/blob/master/images/img_dockerhub.png)

- **Heroku Staging et Production** : Déploiements distincts sur Heroku pour les environnements de staging et de production, montrant la flexibilité d'une infrastructure multi-environnements. Ces captures montrent l'application en cours d'exécution dans des environnements isolés, chacun prêt pour des tests ou une mise en ligne.

![project](https://github.com/omarvm001/Devops-projet-03-JENKINS/blob/master/images/img_heroku.png)

- **Notifications Slack** : Intégration de notifications Slack en temps réel, indiquant le statut de chaque étape du pipeline. Un message de succès en vert confirme le déploiement sans erreurs, tandis qu'un message en rouge alerte immédiatement en cas de problème, permettant une intervention rapide.

![project](https://github.com/omarvm001/Devops-projet-03-JENKINS/blob/master/images/img_slack.png)

Ces résultats confirment que l'ensemble du pipeline CI/CD est opérationnel, assurant un déploiement automatisé, fiable et facilement maintenable de l'application.

## Conclusion

La mise en place de ce pipeline CI/CD pour l'application web statique avec Jenkins a permis d'automatiser les processus de construction, de test et de déploiement, garantissant que chaque mise à jour est rigoureusement testée avant sa mise en production. Grâce à Docker, l'application est non seulement portable, mais bénéficie également d'une gestion cohérente des environnements. L'intégration de Jenkins CI/CD a simplifié le flux de travail et accéléré les déploiements, réduisant ainsi les risques d'erreurs humaines et facilitant la gestion des versions.

Ce pipeline a permis une approche DevOps solide, en assurant un cycle de développement plus agile et des retours d'information rapides grâce aux tests automatisés. Enfin, l'hébergement sur Heroku offre une plateforme flexible pour déployer l'application dans différents environnements (staging, production), garantissant ainsi que l'application est prête avant chaque mise en production.

En résumé, ce projet démontre comment les outils modernes comme Docker, Jenkins, et Heroku peuvent être intégrés efficacement pour améliorer la qualité, la rapidité et la fiabilité des livraisons d'applications.


