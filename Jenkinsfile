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

script {
  sh "scp  -o StrictHostKeyChecking=no  services.yml pods.yml "
  sh "pwd "
  kubernetesDeploy(configs: "node-app-pod.yml", kubeconfigId: "kubeconfig")
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
