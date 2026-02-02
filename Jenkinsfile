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
                mvn clean package -DskipTests
                '''
            }
        }

        stage('Build Docker Image') {
    steps {
        sh 'docker build -t sam840/demo1-app:17 .'

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
              docker push sam840/demo1-app:17
            '''
        }
    }
}



        stage('Deploy to Kubernetes') {
            steps {
                sh '''
                kubectl apply -f demo1-deploy.yaml
                kubectl apply -f demo1-svc.yaml
                kubectl apply -f demo1-ingress.yaml
                '''
            }
        }
    }
}
