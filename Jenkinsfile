pipeline {
    agent any

    environment {
        APP_NAME = "trend-app"
        IMAGE_NAME = "dharineesh01/trend-app:latest"
        EKS_CLUSTER = "trend-cluster"
        AWS_REGION = "ap-south-1"  // Change as per your EKS region
    }

    stages {
        stage('Checkout Code') {
            steps {
                git(
                    url: 'https://github.com/dharineesh16/Trend-App.git',
                    branch: 'main',
                    credentialsId: 'github-credentials' // Your GitHub credential ID
                )
            }
        }

        stage('Build & Push Docker Image') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'dockerhub-credentials', // Your Docker Hub credential ID
                    usernameVariable: 'DOCKER_USERNAME',
                    passwordVariable: 'DOCKER_PASSWORD'
                )]) {
                    sh '''
                        echo $DOCKER_PASSWORD | docker login -u $DOCKER_USERNAME --password-stdin
                        docker build -t $IMAGE_NAME .
                        docker push $IMAGE_NAME
                    '''
                }
            }
        }

        stage('Configure kubeconfig') {
            steps {
                withAWS(region: "${AWS_REGION}", credentials: 'aws-jenkins-credentials') { // AWS credentials ID
                    sh "aws eks update-kubeconfig --name ${EKS_CLUSTER} --region ${AWS_REGION}"
                }
            }
        }

        stage('Deploy to EKS') {
            steps {
                sh '''
                    kubectl apply -f deployment.yaml
                    kubectl apply -f service.yaml
                    kubectl rollout status deployment/trend-app
                '''
            }
        }
    }

    post {
        success {
            echo "✅ Deployment Successful!"
        }
        failure {
            echo "❌ Deployment Failed!"
        }
    }
}
