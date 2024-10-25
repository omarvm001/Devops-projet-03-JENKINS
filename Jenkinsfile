/* import shared library */
@Library('shared-library')_

pipeline {
    environment {
        IMAGE_NAME = "devops-projet-03-jenkins"
        APP_CONTAINER_PORT = "5000"
        APP_EXPOSED_PORT = "80"
        IMAGE_TAG = "latest"
        STAGING = "${ID_DOCKER}-staging"
        PRODUCTION = "${ID_DOCKER}-production"
        DOCKERHUB_ID = "omarvm001" // Utilise 'omarvm001' par défaut si ID_DOCKER_PARAMS n'est pas défini
        DOCKERHUB_PASSWORD = credentials('dockerhub')
    }
    agent none
    stages {
       stage('Build image') {
           agent any
           steps {
              script {
                sh 'docker build -t ${DOCKERHUB_ID}/$IMAGE_NAME:$IMAGE_TAG .'
              }
           }
       }
       stage('Run container based on builded image') {
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
       stage('Test image') {
           agent any
           steps {
              script {
                sh '''
                   curl 172.17.0.1 | grep -i "Dimension"
                '''
              }
           }
       }
       stage('Clean container') {
          agent any
          steps {
             script {
               sh '''
                   docker stop $IMAGE_NAME
                   docker rm $IMAGE_NAME
               '''
             }
          }
      }

      stage ('Login and Push Image on docker hub') {
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
                heroku stack:set container -a $STAGING
                heroku create $STAGING || echo "project already exists"
                heroku container:push web -a $STAGING
                heroku container:release web -a $STAGING
             '''
           }
        }
     }
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
               heroku stack:set container -a $PRODUCTION
               heroku create $PRODUCTION || echo "project already exists"
               heroku container:push web -a $PRODUCTION
               heroku container:release web -a $PRODUCTION
            '''
          }
       }
     }
  }
  post {
       success {
         slackSend (color: '#00FF00', message: "SUCCESSFUL: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]' (${env.BUILD_URL})")
         }
      failure {
            slackSend (color: '#FF0000', message: "FAILED: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]' (${env.BUILD_URL})")
          }   
    }
}
