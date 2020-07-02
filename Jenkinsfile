pipeline {
    agent any
    environment{
        DOCKER_TAG = getDockerTag()
    }
    stages {
        stage('Build Docker Image'){
            steps{
                sh "docker build . -t mgrbic/eqs:${DOCKER_TAG}"
            }
        }
        stage('DockerHub Push'){
            steps{
              withCredentials([string(credentialsId: 'docker-hub', variable: 'dockerHubPwd')]) {
                sh "docker login -u mgrbic -p ${dockerHubPwd}"
                sh "docker push mgrbic/eqs:${DOCKER_TAG}"
              }
            }
        }
        stage('Deploy to kubernetes')
            steps{
                sh "chmod +x tag.sh"
                sh "./tag.sh ${DOCKER_TAG}"
                sshagent(['master-key']) {    
				sh "scp -o StrictHostKeyChecking=no services.yml node-app-pod.yml ubuntu@54.191.253.34:/home/ubuntu/"
               /* script{
                    try{
                        sh "ssh ubuntu@54.191.253.34 kubectl apply -f ."
                    }catch(error){
                        sh "ssh ubuntu@54.191.253.34 kubectl create -f ."
                    }
                }*/
			}
        }
    }
}

def getDockerTag(){
    def tag = sh script: 'git rev-parse HEAD', returnStdout: true
    return tag
}