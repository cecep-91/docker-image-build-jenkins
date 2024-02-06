pipeline {
    environment {
    registry = "10.100.1.140/dev-myproject/mynginx"
    registryCredential = 'harbor'
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
                    sh 'sed -i "s/2.0/${BUILD_NUMBER}/g" index.html'
                    dockerImage = docker.build registry + ":$BUILD_NUMBER"
                }
            }
        }
        stage('Deploy our image') {
            steps{
                script {
                    docker.withRegistry( 'https://10.100.1.140', registryCredential ) {
                        dockerImage.push()
                    }
                }
            }
        }
        stage('Deploy to Kubernetes') {
            steps {
                script {
                    withKubeConfig(credentialsId: 'kubeconfig') {
                        sh 'sed -i "s/1.02/${BUILD_NUMBER}/g" kubernetes/kustomization.yaml'
                        sh "cat kubernetes/kustomization.yaml"
                        sh "kubectl apply -k kubernetes/"
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
