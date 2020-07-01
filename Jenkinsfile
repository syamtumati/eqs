pipeline {
    agent any
    environment{
        DOCKER_TAG = getDockerTag()
    }
    stage('Initialize'){
        def dockerHome = tool 'myDocker'
        env.PATH = "${dockerHome}/bin:${env.PATH}"
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
    }
}

def getDockerTag(){
    def tag = sh script: 'git rev-parse HEAD', returnStdout: true
    return tag
}