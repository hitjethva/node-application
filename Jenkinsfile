pipeline {
    agent any
    environment {
        PROJECT_ID = 'PROJECT-ID'
        CLUSTER_NAME = 'CLUSTER-NAME'
        LOCATION = 'CLUSTER-LOCATION'
        CREDENTIALS_ID = 'vultr'
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
                    myapp = docker.build("sjc.vultrcr.com/jenkinsregistry/node-app:${env.BUILD_ID}")
                }
            }
        }
        stage("Push image") {
            steps {
                script {
                    docker.withRegistry('https://sjc.vultrcr.com/jenkinsregistry', 'vcr-credentials') {
                            myapp.push("latest")
                            myapp.push("${env.BUILD_ID}")
                    }
                }
            }
        }

stage("Deploy to Vultr Kubernetes") {
    steps {
        sh "sed -i 's/node-app:latest/node-app:${env.BUILD_ID}/g' deployment.yml"
                script {
    withKubeConfig([credentialsId: 'kubeconfig']) 
        {
      sh 'kubectl get nodes'
      sh "kubectl apply -f deployment.yml"
      sh "kubectl apply -f service.yml"

      }                
    }
  }
}
        
}
}
