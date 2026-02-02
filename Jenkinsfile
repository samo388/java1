pipeline {
    agent any

    environment {
        IMAGE_NAME = "sam840/demo1-app"
        IMAGE_TAG  = "${BUILD_NUMBER}"
    }

    stages {

        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build Jar') {
            steps {
                sh '''
                  chmod +x mvnw
                  ./mvnw clean package -DskipTests
                '''
            }
        }

        stage('Build Docker Image') {
            steps {
                sh '''
                  docker build -t ${IMAGE_NAME}:${IMAGE_TAG} .
                '''
            }
        }

        stage('Docker Login & Push') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'dockerhub-creds',
                    usernameVariable: 'DOCKER_USER',
                    passwordVariable: 'DOCKER_PASS'
                )]) {

                    sh '''
                      echo "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin
                      docker push ${IMAGE_NAME}:${IMAGE_TAG}
                    '''
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                sh '''
                  kubectl apply -f demo1-deploy.yaml
                  kubectl apply -f demo1-ingress.yaml
                '''
            }
        }
    }
}
