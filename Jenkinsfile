pipeline {
    agent any
    
    tools{
        maven "Maven3"
    }
    environment {
        registry = "118987787108.dkr.ecr.us-east-2.amazonaws.com/cubic_ubuntu_docker_registry" //this is from AWS ECR rpository URI
    }

    stages {
        stage('check out') {
            steps {
               checkout([$class: 'GitSCM', branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[credentialsId: 'Credential', url: 'https://github.com/Shambhu-repo/cubic_EKS_Learn']]])
            }
        }
        stage("build jar fille"){
           
            steps{
              
                sh "mvn clean install"
                
            }
        }
        stage("build docker image"){
            steps{
                script{
                    docker.build registry
                }
                
            }
        }
        stage ("push into ECR"){
            steps{
            sh "aws ecr get-login-password --region us-east-2 | docker login --username AWS --password-stdin 118987787108.dkr.ecr.us-east-2.amazonaws.com"
            sh "docker push 118987787108.dkr.ecr.us-east-2.amazonaws.com/cubic_ubuntu_docker_registry:latest"
            }
        }
        
        stage("k8s deployment"){
            steps{
               withKubeConfig(caCertificate: '', clusterName: '', contextName: '', credentialsId: 'K8S', namespace: '', serverUrl: '')  {
                sh "kubectl apply -f eks-deploy-k8s.yaml"
}
            }
        }
        
    }
}
