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
                withCredentials([file(credentialsId: 'eks-kubeconfig', variable: 'KUBECONFIG_FILE')]) {
                    sh '''
                        mkdir -p $HOME/.kube
                        cp $KUBECONFIG_FILE $HOME/.kube/config
                        chmod 600 $HOME/.kube/config
                        echo "Kubeconfig configured for Jenkins agent"
                    '''
                }
            }
        }

        stage('Deploy to EKS') {
            steps {
                sh '''
                    which kubectl
                    kubectl version --client
                    kubectl config get-contexts
                    kubectl set image deployment/trend-app trend-app=${DOCKER_IMAGE} --record
                    kubectl rollout status deployment/trend-app
                '''
            }
        }
    }
}

