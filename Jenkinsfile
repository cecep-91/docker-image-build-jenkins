pipeline {
    environment {
    registry = "ikubaru/mynginx"
    registryCredential = 'ea46c1b2-f732-4f61-a2fd-b67bade044f2'
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
        stage('Deploy to Kubernetes') {
            steps {
                script {
                    withKubeConfig(credentialsId: 'kubeconfig') {
                        sh "sed "s/mynginx:6/mynginx:${BUILD_NUMBER}/g" kubernetes/deployment.yaml"
                        sh "cat kubernetes/deployment.yaml"
                        sh "kubectl apply -f kubernetes/deployment.yaml"
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
