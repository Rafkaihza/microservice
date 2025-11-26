pipeline {
    agent any

    environment {
        DOCKERHUB_CREDENTIALS = 'dockerhub'
        IMAGE_NAME = 'rafkaihza/micro-frontend'
        K8S_NAMESPACE = 'ecommerce'
    }

    stages {
        stage('Clone Repo') {
            steps {
                git 'https://github.com/GoogleCloudPlatform/microservices-demo.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    dir('src/frontend') {
                        sh """
                        docker build -t ${IMAGE_NAME}:latest .
                        """
                    }
                }
            }
        }

        stage('Push to Docker Hub') {
            steps {
                withCredentials([usernamePassword(credentialsId: DOCKERHUB_CREDENTIALS,
                                                 usernameVariable: 'DOCKER_USER',
                                                 passwordVariable: 'DOCKER_PASS')]) {
                    sh """
                    echo "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin
                    docker push ${IMAGE_NAME}:latest
                    """
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                sh """
                kubectl apply -f ./release/kubernetes-manifests.yaml -n ${K8S_NAMESPACE}
                """
            }
        }
    }
}
