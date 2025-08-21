pipeline {
    agent any

    environment {
        DOCKER_IMAGE = "dharineesh01/trend-app:latest"
        REPO_URL = "https://github.com/dharineesh16/Trend-App.git"
    }

    stages {
        stage('Clone Repo') {
            steps {
                git branch: 'main', url: "${REPO_URL}"
            }
        }

        stage('Build & Push Docker Image') {
            steps {
                script {
                    docker.withRegistry('https://index.docker.io/v1/', 'dockerhub-credentials') {
                        def app = docker.build("${DOCKER_IMAGE}")
                        app.push()
                    }
                }
            }
        }

        stage('Configure kubeconfig') {
            steps {
                withAWS(credentials: 'aws-credentials', region: 'ap-south-1') {
                    sh '''
                        aws eks update-kubeconfig --region ap-south-1 --name trend-cluster
                        kubectl get nodes
                    '''
                }
            }
        }

        stage('Deploy to EKS') {
            steps {
                sh '''
                    kubectl set image deployment/trend-deployment trend-container=${DOCKER_IMAGE} --record
                    kubectl rollout status deployment/trend-deployment
                '''
            }
        }
    }

    post {
        success {
            echo "Deployment succeeded ✅"
        }
        failure {
            echo "Deployment failed ❌"
        }
    }
}
