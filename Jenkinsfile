pipeline {
environment {
registry = "yousry943/erb"
registryCredential = 'dockerhub_id'
SshUser = 'ssh-user'

dockerImage = ''
}
agent any
stages {

stage('Building our image') {
steps{
script {
dockerImage = docker.build registry + ":$BUILD_NUMBER"
}
}
}
stage('Push our image') {
steps{
script {
docker.withRegistry( '', registryCredential ) {
dockerImage.push()
}
}
}
}


stage('Docker Deploy Dev'){
    steps{
        sshagent(['kops-machine']) {




            sh "ssh yousry@127.0.0.1 docker run -d -p 8080:8080 --name nodeapp $BUILD_NUMBER"



  // Remove existing container, if container name does not exists still proceed with the build
  sh script: "ssh yousry@127.0.0.1 docker rm -f nodeapp",  returnStatus: true

            sh "ssh yousry@127.0.0.1 docker run -d -p 8080:8080 --name nodeapp $BUILD_NUMBER"
        }
    }
}


stage('Cleaning up') {
steps{
sh "docker rmi $registry:$BUILD_NUMBER"
}
}
}
}

def getDockerTag(){
    def tag  = sh script: 'git rev-parse HEAD', returnStdout: true
    return tag
}
