pipeline {

    agent any

    environment {
        IMAGE_NAME = "week9-devops-app"
        IMAGE_TAG = "${BUILD_NUMBER}"
        CONTAINER_NAME = "week9-app"
    }

    stages {

        stage('Checkout') {
            steps {
                echo 'Checking out source code from GitHub...'
                checkout scm
            }
        }

        stage('Build') {
            steps {
                echo 'Building application...'
                sh '''
                    test -f app/index.html
                    test -f app/Dockerfile
                    echo "Application build validation successful"
                '''
            }
        }

        stage('Test') {
            steps {
                echo 'Running automated tests...'
                sh '''
                    grep -q "Week 9 DevOps CI/CD Pipeline" app/index.html
                    echo "Application test passed"
                '''
            }
        }

        stage('Docker Build') {
            steps {
                echo 'Building Docker image...'
                sh '''
                    docker build \
                    -t ${IMAGE_NAME}:${IMAGE_TAG} \
                    -t ${IMAGE_NAME}:latest \
                    ./app
                '''
            }
        }

        stage('Deploy') {
            steps {
                echo 'Deploying application...'
                sh '''
                    docker rm -f ${CONTAINER_NAME} 2>/dev/null || true

                    docker run -d \
                    --name ${CONTAINER_NAME} \
                    -p 8081:80 \
                    ${IMAGE_NAME}:${IMAGE_TAG}
                '''
            }
        }

        stage('Verify') {
            steps {
                echo 'Verifying application deployment...'
                sh '''
                    sleep 5
                    curl -f http://localhost:8081
                    echo ""
                    echo "Application deployment verified successfully"
                '''
            }
        }
    }

    post {
        success {
            echo 'CI/CD Pipeline completed successfully!'
        }

        failure {
            echo 'CI/CD Pipeline failed. Check the Jenkins console output.'
        }
    }
}
