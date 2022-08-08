pipeline {

    environment {

        APP_NAME = "demo-app-nodejs-aleman"
        

      // App Repository
        APP_REPOSITORY = "https://github.com/tomasferrarisenda/demo-app-nodejs-aleman.git"

        APP_TAG = "${BUILD_NUMBER}"

        DOCKER_USERNAME = "tferrari92"
        DOCKER_PASSWORD = "hirvyt-werrub-Wemso4"
    }

    agent any
  
    stages {

        stage('Checkout app repo') {
            steps {
              git branch: 'main', changelog: false, poll: false, url: 'https://github.com/tomasferrarisenda/demo-app-nodejs-aleman.git'
            }
        }

        stage('Run npm install') {
           steps {  
                sh 'npm install'
                
            }
        }

        stage('Build docker image') {
            steps {
                sh 'sudo systemctl enable docker.service'
                sh 'sudo systemctl start docker.service'
                sh 'docker build . -t $APP_NAME'
            }
        }

        stage('Push to registry') {
            steps {
                sh 'docker login --username=$DOCKER_USERNAME --password=$DOCKER_PASSWORD'
                sh 'docker tag $APP_NAME $DOCKER_USERNAME/$APP_NAME:$APP_TAG'
                sh 'docker push $DOCKER_USERNAME/$APP_NAME:$APP_TAG'
            }
        }

    }

    post{
        always {  
	        deleteDir()     
        }      
    }
}

