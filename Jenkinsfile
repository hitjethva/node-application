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
                sh "sed -i 's/node-app:latest/node-app:${env.BUILD_ID}/g' deployment.yml"
                step([$class: 'KubernetesEngineBuilder', projectId: env.PROJECT_ID, clusterName: env.CLUSTER_NAME, location: env.LOCATION, manifestPattern: 'deployment.yml', credentialsId: env.CREDENTIALS_ID, verifyDeployments: true])
            }
        }
    }
}
