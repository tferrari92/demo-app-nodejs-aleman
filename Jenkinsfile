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


    // agent {
    //     docker { image 'tferrari92/jenkins-inbound-agent-git-npm-docker' }
    // }
  
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
              //  Estos pasos hay q hacerlos manuales en el host
                // systemctl enable docker.service
                // systemctl start docker.service
                // sudo usermod -a -G docker jenkins
                // sudo service jenkins restart
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
                node {
                    def remote = [:]
                    remote.name = '192.168.122.116'
                    remote.host = '192.168.122.116'
                    remote.user = 'root'
                    remote.password = 'sendati123'
                    remote.allowAnyHosts = true
                    stage('Run container') {
                        sshCommand remote: remote, command: "docker run tferrari92/$APP_NAME:$APP_TAG"
                    }
                }
            }    
        }

        // stage('Run app docker image') {
        //     steps {
        //         sh 'sudo docker run tferrari92/$APP_NAME:$APP_TAG'
        //     }
        // }

    }

    post{
        always {  
	        deleteDir()     
        }      
    }
}

