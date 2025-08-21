pipeline {
    agent any

    environment {
        DOCKER_IMAGE = "dharineesh01/trend-app:latest"
        AWS_REGION   = "ap-south-1"
        EKS_CLUSTER  = "trend-cluster"
    }

    stages {
        stage('Checkout Code') {
            steps {
                git branch: 'main', url: 'https://github.com/dharineesh16/Trend-App.git'
            }
        }

        stage('Build & Push Docker Image') {
            steps {
                script {
                    withCredentials([usernamePassword(
                        credentialsId: 'dockerhub-credentials:'dckr_pat_TjH_OlQLo82LnqnkViDxi-SX73o'', 
                        usernameVariable: 'DOCKER_USER', 
                        passwordVariable: 'DOCKER_TOKEN')]) {
                        
                        // Docker login using Personal Access Token
                        sh 'echo $DOCKER_TOKEN | docker login -u $DOCKER_USER --password-stdin'
                        
                        // Build Docker image
                        sh "docker build -t $DOCKER_IMAGE ."
                        
                        // Push Docker image to Docker Hub
                        sh "docker push $DOCKER_IMAGE"
                    }
                }
            }
        }

        stage('Configure kubeconfig') {
            steps {
                withCredentials([
                    [$class: 'AmazonWebServicesCredentialsBinding',
                     credentialsId: 'aws-credentials']] ) {
                    sh "aws eks --region ap-south-1 update-kubeconfig --name trend-cluster"
                }
            }
        }

        stage('Deploy to EKS') {
            steps {
                sh """
                    kubectl apply -f deployment.yaml
                    kubectl apply -f service.yaml
                    kubectl rollout status deployment/trend-deployment
                """
            }
        }
    }

    post {
        success {
            echo 'Deployment succeeded ✅'
        }
        failure {
            echo 'Deployment failed ❌'
        }
    }
}

