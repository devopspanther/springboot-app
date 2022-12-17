pipeline {
   tools {
        maven 'Maven3'
    }
    agent any
    environment {
        registry = "406187633378.dkr.ecr.us-east-2.amazonaws.com/docker-repo"
    }
   
    stages {
        stage ('Build') {
            steps {
                sh 'mvn clean install'           
                }
        }
        // Building Docker images
        stage('Building image') {
        steps{
            script {
            dockerImage = docker.build registry 
            }
        }
        }
   
        // Uploading Docker images into AWS ECR
        stage('Pushing to ECR') {
        steps{  
            script {
                    sh 'aws ecr get-login-password --region us-east-2 | docker login --username AWS --password-stdin 406187633378.dkr.ecr.us-east-2.amazonaws.com'
                    sh 'docker push 406187633378.dkr.ecr.us-east-2.amazonaws.com/docker-repo:1.0'
            }
            }
        }
        
        stage('K8S Deploy') {
            steps{   
                script {
                    withKubeConfig(caCertificate: '', clusterName: '', contextName: '', credentialsId: 'K8S', namespace: '', serverUrl: '') {
                    sh ('kubectl apply -f  eks-deploy-k8s.yaml')
                    }
                }
            }
        }

    }
}
