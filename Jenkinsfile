pipeline {
    agent any

    environment {
        DOCKER_IMAGE = "dharineesh01/trend-app:latest"
        REPO_URL = "https://github.com/dharineesh16/trend-app.git"
    }

    stages {
        stage('Clone Repo') {
            steps {
                git branch: 'main', url: "${REPO_URL}"
            }
        }

        stage('Deploy to EKS') {
            steps {
                // Update Kubernetes deployment to use pre-built Docker image
                sh '''
                    kubectl set image deployment/trend-app trend-app=${DOCKER_IMAGE} --record
                    kubectl rollout status deployment/trend-app
                '''
            }
        }
    }
}