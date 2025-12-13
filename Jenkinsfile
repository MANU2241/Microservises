pipeline {
    agent any

    tools {
        maven 'M3'
    }

    environment {
        DOCKERHUB_USERNAME = "your_dockerhub_username"
        DOCKERHUB_CREDENTIALS = credentials('dockerhub-creds')
    }

    stages {

        stage('Checkout Code') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/MANU2241/Microservises.git'
            }
        }

        stage('Build Maven Projects') {
            steps {
                sh 'mvn clean package -DskipTests'
            }
        }

        stage('Build Docker Images') {
            steps {
                sh '''
                docker build -t $DOCKERHUB_USERNAME/service-registry service-registry
                docker build -t $DOCKERHUB_USERNAME/api-gateway api-gateway
                docker build -t $DOCKERHUB_USERNAME/question-service question-service
                docker build -t $DOCKERHUB_USERNAME/quiz-service quiz-service
                '''
            }
        }

        stage('Push Images to Docker Hub') {
            steps {
                sh '''
                echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_USERNAME --password-stdin
                docker push $DOCKERHUB_USERNAME/service-registry
                docker push $DOCKERHUB_USERNAME/api-gateway
                docker push $DOCKERHUB_USERNAME/question-service
                docker push $DOCKERHUB_USERNAME/quiz-service
                '''
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                sh '''
                kubectl apply -f k8s/
                '''
            }
        }
    }

    post {
        success {
            echo "✅ CI/CD Pipeline Completed Successfully!"
        }
        failure {
            echo "❌ CI/CD Pipeline Failed!"
        }
    }
}
