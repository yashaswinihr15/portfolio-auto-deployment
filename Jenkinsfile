pipeline {
    agent any

    stages {

        stage('Build Docker Image') {
            steps {
                echo 'Building Docker image...'

                sh '''
                    docker build --no-cache \
                    -t portfolio:${BUILD_NUMBER} \
                    -t portfolio:latest \
                    ${WORKSPACE}
                '''
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                echo 'Deploying to Kubernetes...'

                sh '''
                    export KUBECONFIG=/var/jenkins_home/.kube/config

                    kubectl config view --minify
                    kubectl apply -f ${WORKSPACE}/k8s/

                    kubectl set image deployment/portfolio-deployment \
                    portfolio=portfolio:${BUILD_NUMBER}
                '''
            }
        }

        stage('Verify Deployment') {
            steps {
                echo 'Verifying Kubernetes deployment...'

                sh '''
                    export KUBECONFIG=/var/jenkins_home/.kube/config

                    kubectl rollout status deployment/portfolio-deployment
                    kubectl get pods
                    kubectl get services
                '''
            }
        }
    }

    post {
        success {
            echo 'Portfolio deployed successfully!'
        }

        failure {
            echo 'Portfolio deployment failed. Check Jenkins console output.'
        }
    }
}