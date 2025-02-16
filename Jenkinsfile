pipeline {
    agent any

    environment {
        DOCKER_IMAGE = 'ecommerce-app'
        REGISTRY_URL = '<IBM_Container_Registry_URL>'
        CLUSTER_NAME = '<IBM_Kubernetes_Cluster_Name>'
    }

    stages {
        stage('Checkout') {
            steps {
                git 'https://github.com/<your-username>/ecommerce-app.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    sh 'docker build -t $REGISTRY_URL/$DOCKER_IMAGE .'
                }
            }
        }

        stage('Push Docker Image to IBM Cloud Container Registry') {
            steps {
                script {
                    sh 'docker push $REGISTRY_URL/$DOCKER_IMAGE'
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                script {
                    sh '''
                    ibmcloud login --apikey <API_KEY> -r <REGION> -g <RESOURCE_GROUP>
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
