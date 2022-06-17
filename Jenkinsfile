pipeline {
     environment {
       ID_DOCKER = "nicolasdev1990"
       PASS_DOCKER = "SuperPassword9."
       IMAGE_NAME = "alpinehelloworld"
       IMAGE_TAG = "latest"
       STAGING = "${ID_DOCKER}-staging"
       PRODUCTION = "${ID_DOCKER}-production"
     }
     agent none
     stages {
         stage('Build image') {
             agent any
             steps {
                script {
                  sh 'docker build -t ${ID_DOCKER}/${IMAGE_NAME}:${IMAGE_TAG} .'
                }
             }
        }
        stage('Run container based on builded image') {
            agent any
            steps {
               script {
                 sh 'docker run -d -p 8082:8082 -e PORT=8082 --name ${IMAGE_NAME} ${ID_DOCKER}/${IMAGE_NAME}:${IMAGE_TAG}'
                 sh 'sleep 5'
               }
            }
       }
       stage('Test image') {
           agent any
           steps {
              script {
                sh ''
              }
           }
      }
      stage('Clean Container') {
          agent any
          steps {
             script {
               sh 'docker stop ${IMAGE_NAME}'
                sh 'docker rm  ${IMAGE_NAME}'
             }
          }
     }

     stage ('Login and Push Image on docker hub') {
          agent any
          steps {
             script {
               sh 'docker login -u ${ID_DOCKER} -p ${PASS_DOCKER}'
               sh 'docker image push ${ID_DOCKER}/${IMAGE_NAME}:${IMAGE_TAG}'
             }
          }
      }    
     
stage('Push image in staging and deploy it') {
       when {
              expression { GIT_BRANCH == 'origin/master' }
            }
      agent any
      environment {
          HEROKU_API_KEY = credentials('heroku_api_key')
      }  
      steps {
          script {
            sh '''
              heroku container:login
              heroku create $STAGING || echo "project already exist"
              heroku container:push -a $STAGING web
              heroku container:release -a $STAGING web
            '''
          }
        }
     }



     stage('Push image in production and deploy it') {
       when {
              expression { GIT_BRANCH == 'origin/master' }
            }
      agent any
      environment {
          HEROKU_API_KEY = credentials('heroku_api_key')
      }  
      steps {
          script {
            sh '''
              heroku container:login
              heroku create $PRODUCTION || echo "project already exist"
              heroku container:push -a $PRODUCTION web
              heroku container:release -a $PRODUCTION web
            '''
          }
        }
     }
  }
}
