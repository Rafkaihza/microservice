pipeline {
    agent any

    environment {
        DOCKERHUB_CREDENTIALS = credentials('dockerhub-cred-id') // id di Jenkins
        DOCKER_REPO = 'rafkaihza78'
        KUBE_CONTEXT = 'minikube'
        HELM_RELEASE = 'onlineboutique'
        HELM_CHART_PATH = './release'
        NAMESPACE = 'ecommerce'
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/USERNAME/microservices-demo.git'
            }
        }

        stage('Build Docker Images') {
            steps {
                sh '''
                docker build -t $DOCKER_REPO/productcatalogservice:${BUILD_NUMBER} src/productcatalogservice
                docker build -t $DOCKER_REPO/frontend:${BUILD_NUMBER} src/frontend
                '''
            }
        }

        stage('Push Images') {
            steps {
                sh '''
                echo "$DOCKERHUB_CREDENTIALS_PSW" | docker login -u "$DOCKERHUB_CREDENTIALS_USR" --password-stdin
                docker push $DOCKER_REPO/productcatalogservice:${BUILD_NUMBER}
                docker push $DOCKER_REPO/frontend:${BUILD_NUMBER}
                '''
            }
        }

        stage('Deploy with Helm') {
            steps {
                sh '''
                kubectl config use-context $KUBE_CONTEXT
                helm upgrade --install $HELM_RELEASE $HELM_CHART_PATH \
                  --namespace $NAMESPACE \
                  --set productcatalog.image=$DOCKER_REPO/productcatalogservice:${BUILD_NUMBER} \
                  --set frontend.image=$DOCKER_REPO/frontend:${BUILD_NUMBER}
                '''
            }
        }
    }
}
