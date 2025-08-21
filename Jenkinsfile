pipeline {
    agent any

    environment {
        DOCKER_IMAGE = "dharineesh01/trend-app:latest"
        REPO_URL = "https://github.com/dharineesh16/trend-app.git"
        PATH = "/usr/local/bin:/usr/bin:/bin"
    }

    stages {
        stage('Clone Repo') {
            steps {
                git branch: 'main', url: "${REPO_URL}"
            }
        }

        stage('Configure kubeconfig') {
            steps {
                withCredentials([[$class: 'AmazonWebServicesCredentialsBinding', credentialsId: 'aws-creds']]) {
                    sh '''
                        aws eks --region ap-south-1 update-kubeconfig --name trend-cluster
                        kubectl config current-context
                    '''
                }
            }
        }

        stage('Deploy to EKS') {
            steps {
                sh '''
                    which kubectl
                    kubectl version --client
                    kubectl get nodes
                    kubectl set image deployment/trend-app trend-app=${DOCKER_IMAGE} --record -n default
                    kubectl rollout status deployment/trend-app -n default
                '''
            }
        }
    }
}

