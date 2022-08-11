def remote = [:]
remote.name = '192.168.122.116'
remote.host = '192.168.122.116'
remote.user = 'server'
remote.password = 'server'
remote.allowAnyHosts = true

pipeline {

    environment {

        APP_NAME = "demo-app-nodejs-aleman"
        
        APP_REPOSITORY = "https://github.com/tomasferrarisenda/demo-app-nodejs-aleman.git"

        APP_TAG = "${BUILD_NUMBER}"

        DOCKER_USERNAME = "tferrari92"
        DOCKER_PASSWORD = "hirvyt-werrub-Wemso4"
    }

    agent any
// borrar esto
  
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
                // En el host hay q habiliar jenkins para usar docker:
                //      1. Habilitar al usuario a usar Docker:
                //          a. $ sudo groupadd docker 
                //          b. $ sudo usermod -aG docker jenkins
                //          c. Restart
                //      2. Configurar la VM para q inicialice Docker al bootear:
                //          a. $ sudo systemctl enable docker.service
                //          b. $ sudo systemctl enable containerd.service
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

        stage('Connect to other VM through SSH and run container') {
            steps {
                // En el server hay q habiliar al usuario para usar docker:
                //      1. Habilitar al usuario a usar Docker:
                //          a. $ sudo groupadd docker 
                //          b. $ sudo usermod -aG docker <nombre de usuario aqui>
                //          c. Restart
                //      2. Configurar la VM para q inicialice Docker al bootear:
                //          a. $ sudo systemctl enable docker.service
                //          b. $ sudo systemctl enable containerd.service
                sshCommand remote: remote, command: "docker run -d $DOCKER_USERNAME/$APP_NAME:$APP_TAG"
            }    
        }
    }

    post{
        always {  
	        deleteDir()     
        }      
    }
}

