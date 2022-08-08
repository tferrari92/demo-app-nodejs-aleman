pipeline {

    environment {

        APP_NAME = "demo-app-nodejs-aleman"

      // App Repository
        APP_REPOSITORY = "https://github.com/tomasferrarisenda/demo-app-nodejs-aleman.git"
        APP_REPO_DIRECTORY = "${WORKSPACE}/demo-app-nodejs-aleman"

        APP_TAG = "${BUILD_NUMBER}"

        DOCKER_USERNAME = "tferrari92"
        DOCKER_PASSWORD = "hirvyt-werrub-Wemso4"
    }

    agent any
  
    stages {

        stage('Clonar repo') {
            steps {
              git branch: 'main', changelog: false, poll: false, url: 'https://github.com/tomasferrarisenda/demo-app-nodejs-aleman.git'
            }
        }

        stage('Correr npm install') {
           steps {  
                dir("${APP_REPO_DIRECTORY}") {
                    sh 'chown -R `whoami` /usr/local/lib/node_modules'
                    sh 'npm install'
                }
            }
        }

        stage('Buildear la imagen') {
            steps {
                sh 'docker build . -t $APP_NAME'
            }
        }

        stage('Pushear imagen a repo personal') {
            steps {
                sh 'docker login --username=$DOCKER_USERNAME --password=$DOCKER_PASSWORD'
                sh 'docker tag $APP_NAME $DOCKER_USERNAME/$APP_NAME:$APP_TAG'
                sh 'docker push $DOCKER_USERNAME/$APP_NAME:$APP_TAG'
            }
        }

    }
}

