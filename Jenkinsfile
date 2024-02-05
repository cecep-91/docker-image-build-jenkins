pipeline {
    environment {
    registry = "ikubaru/mynginx"
    registryCredential = 'ikubaru/******'
    dockerImage = ''
    }

    agent any

    stages {
        stage('Cloning our Git') {
            steps {
                git branch: 'main', url: 'https://github.com/cecep-91/docker-image-build-jenkins.git'
            }
        }
        stage('Building our image') {
            steps{
                script {
                    dockerImage = docker.build registry + ":$BUILD_NUMBER"
                }
            }
        }
        stage('Deploy our image') {
            steps{
                script {
                    docker.withRegistry( '', registryCredential ) {
                        dockerImage.push()
                    }
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
