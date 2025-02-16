pipeline {
    agent any

    environment {
        DOCKER_IMAGE = 'ecommerce-app'
        REGISTRY_URL = credentials('IBM_REGISTRY_URL') // Use Jenkins credentials
        CLUSTER_NAME = credentials('IBM_K8S_CLUSTER')
        API_KEY = credentials('IBM_CLOUD_API_KEY')
        REGION = 'us-south'
        RESOURCE_GROUP = 'default'
    }

    stages {
        stage('Checkout') {
            steps {
                git 'https://github.com/<your-username>/ecommerce-app.git'
            }
        }

        stage('Lint Dockerfile') {
            steps {
                script {
                    sh 'hadolint Dockerfile'
                }
            }
        }

        stage('Build & Push Docker Image') {
            steps {
                script {
                    sh """
                    docker build -t $REGISTRY_URL/$DOCKER_IMAGE .
                    docker push $REGISTRY_URL/$DOCKER_IMAGE
                    """
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                script {
                    sh '''
                    ibmcloud login --apikey $API_KEY -r $REGION -g $RESOURCE_GROUP
                    ibmcloud ks cluster config --cluster $CLUSTER_NAME
                    kubectl apply -f k8s/deployment.yaml
                    '''
                }
            }
        }
    }

    post {
        success {
            echo 'Pipeline executed successfully.'
        }
        failure {
            echo 'Pipeline failed. Check logs for details.'
        }
    }
}