pipeline {
    agent any
    environment{
        DOCKER_TAG = getDockerTag()
        NEXUS_URL  = "127.0.0.1:8080"
        IMAGE_URL_WITH_TAG = "${NEXUS_URL}/node-app:${DOCKER_TAG}"
    }
    stages{
        stage('Build Docker Image'){
            steps{
                sh "docker build . -t ${IMAGE_URL_WITH_TAG}"
            }
        }
        stage('docker  Push'){
            steps{
                withCredentials([string(credentialsId: 'docker-pwd', variable: 'docker-hub')]) {
                    sh "sudo docker login -u yousry943 -p ${docker-hub} "
                }
                sh "sudo docker push yousry943/erb:0.1"
            }
        }
  
    }
}

def getDockerTag(){
    def tag  = sh script: 'git rev-parse HEAD', returnStdout: true
    return tag
}
