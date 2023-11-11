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

stage('List pods') {
    withKubeConfig([credentialsId: 'kubeconfig',
                    caCertificate: 'LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSURnVENDQW1tZ0F3SUJBZ0lJREtEem5mRlBsZEV3RFFZSktvWklodmNOQVFFTEJRQXdUekVMTUFrR0ExVUUKQmhNQ1ZWTXhGakFVQmdOVkJBY1REVk5oYmlCR2NtRnVZMmx6WTI4eEV6QVJCZ05WQkFvVENrdDFZbVZ5Ym1WMApaWE14RXpBUkJnTlZCQU1UQ2t0MVltVnlibVYwWlhNd0hoY05Nak14TVRFeE1ETXdNREF5V2hjTk1qZ3hNVEV4Ck1ETXdNREF5V2pCUE1Rc3dDUVlEVlFRR0V3SlZVekVXTUJRR0ExVUVCeE1OVTJGdUlFWnlZVzVqYVhOamJ6RVQKTUJFR0ExVUVDaE1LUzNWaVpYSnVaWFJsY3pFVE1CRUdBMVVFQXhNS1MzVmlaWEp1WlhSbGN6Q0NBU0l3RFFZSgpLb1pJaHZjTkFRRUJCUUFEZ2dFUEFEQ0NBUW9DZ2dFQkFPQTl3ekduaW81RWN2cERTQ3VVbWJ3M1MreW56U2wvCmJKNmRZaVkyK3NHTHUzNDRZK3pRYlVCek1TOENNMXg0bFpCb29aWjBjUHVXb1hIVnh3b3V2Y3FVS0FhM2pHY1IKNnJHR3FoVlhRV29tTjdYa0hsdDFBMWpRVmFoSG1Fb2FtQXBNZWtQeENkRDVFd3JUR1dybzRvaytMOWM4aHArOApzamxLLzZ6RkxrWFFhc3BEVGtSWmVFb0NCUnpJVEVjZTE2UnFQQUtVYXpEa05WRXh6anNndUdSN242M3ROSGFHCnRCcUkvY1VzRDVsVjFjVTZHLytqaDQyNHIxMXF3WTJWZnRLaFFiZTNYSDFxMGRnSG5MRFluUGpPQjF6QlFEQjEKOFp2aGxDTUpHcnNlenhDYmV2TlFZUi8vMlBXTGVDOGI0UEtheXphaHlEbjBiVEVDL0RGVTQza0NBd0VBQWFOaApNRjh3RGdZRFZSMFBBUUgvQkFRREFnS0VNQjBHQTFVZEpRUVdNQlFHQ0NzR0FRVUZCd01DQmdnckJnRUZCUWNECkFUQVBCZ05WSFJNQkFmOEVCVEFEQVFIL01CMEdBMVVkRGdRV0JCVHkwSzJ2U01IRkl6Snl1YTBoNHNTeUxFYXcKY1RBTkJna3Foa2lHOXcwQkFRc0ZBQU9DQVFFQU5qM1lZVDBpcGg5bEpybXRiU3FoekpISWQvN254bFZOZDZrUApQcFM5UGJMQjVJRUppdlV6RndkaWg0QktHbTN5TGtwbUF6a0VudTRTTEdSWm5Vc2JqU1hGamJhS29aNFVUcjhaCllGTFFHcTNqWFhIaEFxcFdnWFM3UHJHejVHMTJJcXQyTi9BZnRpQXZvQllsWng3dVJsczN1dlhBOWhYNFVRVjYKMmEvQzJ4WE5qZVNYdnFuYlZ1dUpWSUtRdS9HeVFYRlF0K3RiVGdMMUxZVzI0VXBrQUJrdUZRK3NSZkRGOWF4cQpmYUlJaWFwREduTFVoSnc3RWlaZWhxNk51djdseE9oUFNJSE5NWUFaT0xqNnBCMitvd1NOOVF0bW4wTWhRRmQ5CnUvNDNZYnp6c25pWjFRY1pGK0IwZHN3MWx5dmFZODJ1VlJSUS9ubGMzUnVCNzhscjJBPT0KLS0tLS1FTkQgQ0VSVElGSUNBVEUtLS0tLQo=',
                    serverUrl: 'https://f8b207b8-1f1a-4781-a86b-d3c8c7f3bc60.vultr-k8s.com:6443',
                    contextName: 'vke-f8b207b8-1f1a-4781-a86b-d3c8c7f3bc60',
                    clusterName: 'vke-f8b207b8-1f1a-4781-a86b-d3c8c7f3bc60',
                    namespace: 'default'
                    ]) {
      sh 'kubectl get nodes'
      }                                    
        
        stage('Deploy to GKE') {
            steps{
                sh "sed -i 's/node-app:latest/node-app:${env.BUILD_ID}/g' deployment.yml"
                script {
                    withCredentials([usernamePassword(credentialsId: kubeconfig)]) {

                        sh "kubectl apply -f deployment.yml"
                        sh "kubectl apply -f service.yml"
                    }
                    }
            }
        }
    }
}
