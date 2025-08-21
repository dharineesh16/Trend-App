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
                        export KUBECONFIG=$HOME/.kube/config
                        kubectl config use-context arn:aws:eks:ap-south-1:683342011355:cluster/trend-cluster
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
