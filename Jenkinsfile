pipeline {
    agent any
    environment {
        PROJECT_ID = 'vultr'
        CLUSTER_NAME = 'cluster'
        LOCATION = 'usa'
        CREDENTIALS_ID = 'kubeconfig'
    }
    stages {
        stage("Checkout code") {
            steps {
                checkout scm
            }
        }
        stage("Build image") {
            steps {
                script {
                    myapp = docker.build("hitjethva/node-app:${env.BUILD_ID}")
                }
            }
        }
        stage("Push image") {
            steps {
                script {
                    docker.withRegistry('https://registry.hub.docker.com', 'dockerhub-credential') {
                            myapp.push("latest")
                            myapp.push("${env.BUILD_ID}")
                    }
                }
            }
        }
        stage('Deploy to Vultr') {
            steps{
            
kubernetesDeploy(configs: "deployment.yml", "service.yml", kubeconfigId: 'kubeconfig')
            }
        }
    }
}
