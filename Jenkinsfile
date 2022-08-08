pipeline {

    environment {

      // Kubernetes
        APP_NAME = "demo-app"
        NAMESPACE = "default"
        AGENT_IMAGE = "tferrari92/jenkins-inbound-agent-git-npm-docker"

      // App Repository
        APP_REPOSITORY = "https://github.com/tomasferrarisenda/demo-app-nodejs.git"
        APP_REPO_DIRECTORY = "${WORKSPACE}/demo-app-nodejs"

      // Infra Repository
        INFRA_REPOSITORY = "https://github.com/tomasferrarisenda/repo-infra.git"
        INFRA_REPO_DIRECTORY = "${WORKSPACE}/repo-infra"

        APP_TAG = "${BUILD_NUMBER}"

        DOCKER_USERNAME = "tferrari92"
        DOCKER_PASSWORD = "hirvyt-werrub-Wemso4"

        GIT_EMAIL = "tomas.ferrari@sendati.com"
        GIT_USERNAME = "tomasferrarisenda"
        GIT_REPO = "tomasferrarisenda/mock-repo-infra.git"
        GITHUB_CREDENTIALS_ID = "tomasferrarisendaGithubCredentials"
    }

    agent {
        kubernetes {
            yaml '''
apiVersion: v1
kind: Pod
metadata:
  labels:
    jenkins: slave
  name: agent-pod
spec:
  containers:
  - name: agent-container
    image: tferrari92/jenkins-agent-docker-git-npm
    command:
    - sleep
    args:
    - infinity
    resources:
      limits: {}
      requests:
        memory: "256Mi"
        cpu: "100m"
    volumeMounts:
    - mountPath: /home/jenkins/agent
      name: workspace-volume
      readOnly: false
  hostNetwork: false
  nodeSelector:
    kubernetes.io/os: "linux"
  restartPolicy: Never
  volumes:
  - emptyDir:
      medium: ""
    name: workspace-volume

'''
            defaultContainer 'agent-container'
        }
    }

  
    stages {

        stage('Clonar repo') {
            steps {
              sh 'git clone $APP_REPOSITORY'
            }
        }

        // stage('Correr npm install') {
        //     steps {
        //         sh 'npm install'
        //     }
        // }

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

        stage('Descargar repo de infra') {
            steps {
                sh 'git clone $INFRA_REPOSITORY'
            }
        }


        stage('Modificar deployment') {
           steps {  
                dir("${INFRA_REPO_DIRECTORY}") {
                    sh 'rm dev/deployment.yaml'
                    sh '''echo  \"apiVersion: apps/v1
kind: Deployment
metadata: 
  name: $APP_NAME-deployment
spec:
  selector:
    matchLabels:
      app: $APP_NAME
  replicas: 2
  template: 
    metadata:
      labels: 
        app: $APP_NAME
    spec: 
      containers:
      - name: $APP_NAME
        image: $DOCKER_USERNAME/$APP_NAME:$APP_TAG
        ports:
        - containerPort: 8080 \" > dev/deployment.yaml'''
                }
            }
        }


        stage('Pushear los cambios al repo de infra') {
           steps {  
                dir("${INFRA_REPO_DIRECTORY}") {
                    sh 'git config --global user.email "$GIT_EMAIL"'
                    sh 'git config --global user.name "$GIT_USERNAME"'
                    sh 'git add .'
                    sh 'git commit -m "Actualizacion de imagen"'
                    withCredentials([usernamePassword(credentialsId: "${GITHUB_CREDENTIALS_ID}", passwordVariable: 'GIT_PASSWORD', usernameVariable: 'GIT_USERNAME')]) {
                        sh('git push https://${GIT_USERNAME}:${GIT_PASSWORD}@github.com/${GIT_REPO}')
                    }
                }
            }
        }
    }
}

