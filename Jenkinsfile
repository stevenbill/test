pipeline {
environment {
registry = "yousry943/erb"
registryCredential = 'dockerhub_id'
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

stage('Deploy to k8s') {
steps{

sh "chmod +x changeTag.sh"
sh "./changeTag.sh $BUILD_NUMBER"
sshagent(['kops-machine']) {
  script{

  sh "ssh yousry@127.0.0.1 pwd "

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
