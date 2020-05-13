pipeline {
    agent any
    environment{
        DOCKER_TAG = getDockerTag()
    }
    stages{
       stage('build docker  image'){
          steps{
            sh "docker build . -t yousry943/erb:${DOCKER_TAG}"
          }
       }
       stage('Docker hub  push '){
          steps{
            withCredentials([string(CredentialsId: 'docker-hub', variable:'dockerhubpwd')]){
            sh "docker login -u yousry943 -p ${dockerhubpwd}"
            sh "docker push yousry943/erb:${DOCKER_TAG}"

            }
          }
       }
       stage('Deploy  to  k8s'){
       steps{
       sh "chmod +x changeTag.sh"
       sh "./changeTag.sh ${DOCKER_TAG}"
       sshagent(['kops-machine']) {
sh"scp -o StrictHostKeyChecking=no services.yml node-app-pod.yml yousry@127.0.0.1:/home/yousry"

          script{
            try{
            sh "ssh yousry@127.0.0.1 kubectl apply -f ."
            }catch(error){
            sh "ssh yousry@127.0.0.1 kubectl create -f ."

            }
          }
            }
       }

       }



    }

}
