pipeline {
    agent any

    environment {
        IMAGE_NAME = "samo388/demo1-app"
        IMAGE_TAG  = "${env.BUILD_NUMBER}"
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
        sh 'docker build -t $IMAGE_NAME:latest .'
    }
}


        stage('Docker Login & Push') {
    steps {
        sh '''
          echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin
          docker push $IMAGE_NAME:$IMAGE_TAG
        '''
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
