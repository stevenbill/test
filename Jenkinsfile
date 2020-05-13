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
                withCredentials([string(credentialsId: 'docker-hub', variable: 'nexusPwd')]) {
                    sh "docker login -u yousry943  "
                    sh "docker push yousry943/erb:${IMAGE_URL_WITH_TAG}"
                }
            }
        }
        stage('Docker Deploy Dev'){
            steps{
                  sshagent(['kops-machine']) {
                    withCredentials([string(credentialsId: 'docker-hub', variable: 'nexusPwd')]) {
                        sh "ssh yousry@127.0.0.1 docker login -u admin -p ${nexusPwd} ${NEXUS_URL}"
                    }
					// Remove existing container, if container name does not exists still proceed with the build
					sh script: "ssh yousry@127.0.0.1 docker rm -f nodeapp",  returnStatus: true

                    sh "ssh yousry@127.0.0.1 docker run -d -p 8080:8080 --name nodeapp ${IMAGE_URL_WITH_TAG}"
                }
            }
        }
    }
}

def getDockerTag(){
    def tag  = sh script: 'git rev-parse HEAD', returnStdout: true
    return tag
}
