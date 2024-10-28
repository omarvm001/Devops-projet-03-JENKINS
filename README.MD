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

## Workflow du Pipeline CI/CD

Ce projet utilise un pipeline CI/CD pour automatiser le processus de développement et de déploiement de l'application. Voici un aperçu des étapes clés du workflow et leurs conditions d'exécution.


More info [here](https://html5up.net/license).
