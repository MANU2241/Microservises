pipeline {
    agent any
    tools {
        maven 'M3'
    }

    environment {
        DOCKERHUB_USERNAME = "YOUR_USER_NAME"
        DOCKERHUB_CREDENTIALS = credentials('dockerhub-creds')
    }

    stages {

        stage('Checkout Code') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/MANU2241/Microservises.git'
            }
        }

        stage('Build Service Registry') {
            steps {
                bat 'cd service-registry && mvn clean package -DskipTests'
            }
        }
        
        stage('Build API Gateway') {
            steps {
                bat 'cd api-gateway && mvn clean package -DskipTests'
            }
        }
        
        stage('Build Question Service') {
            steps {
                bat 'cd question-service && mvn clean package -DskipTests'
            }
        }
        
        stage('Build Quiz Service') {
            steps {
                bat 'cd quiz-service && mvn clean package -DskipTests'
            }
        }

        
        stage('Verify JARs') {
            steps {
                bat '''
                dir service-registry\\target
                dir api-gateway\\target
                dir question-service\\target
                dir quiz-service\\target
                '''
            }
        }



        stage('Build Docker Images') {
            steps {
                bat '''
                docker build -t %DOCKERHUB_USERNAME%/service-registry service-registry
                docker build -t %DOCKERHUB_USERNAME%/api-gateway api-gateway
                docker build -t %DOCKERHUB_USERNAME%/question-service question-service
                docker build -t %DOCKERHUB_USERNAME%/quiz-service quiz-service
                '''
            }
        }

        stage('Push Images to Docker Hub') {
            steps {
                bat '''
                docker login -u %DOCKERHUB_USERNAME% -p %DOCKERHUB_CREDENTIALS_PSW%
                docker push %DOCKERHUB_USERNAME%/service-registry
                docker push %DOCKERHUB_USERNAME%/api-gateway
                docker push %DOCKERHUB_USERNAME%/question-service
                docker push %DOCKERHUB_USERNAME%/quiz-service
                '''
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                bat 'kubectl apply -f k8s'
            }
        }
    }

    post {
        success {
            echo '✅ CI/CD Pipeline Completed Successfully!'
        }
        failure {
            echo '❌ CI/CD Pipeline Failed!'
        }
    }
}
