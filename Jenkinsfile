pipeline {

    agent any

  environment {
    DOCKERHUB_USERNAME = "phrr"
    IMAGE_NAME = "${DOCKERHUB_USERNAME}/week9-devops-app"
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
        stage('Docker Push') {
            steps {
              echo 'Pushing Docker image to Docker Hub...'

                withCredentials([
                usernamePassword(
                credentialsId: 'dockerhub-creds',
                usernameVariable: 'DOCKER_USERNAME',
                passwordVariable: 'DOCKER_PASSWORD'
                 )
                 ]) {
                sh '''
                echo "$DOCKER_PASSWORD" | docker login \
                    -u "$DOCKER_USERNAME" \
                    --password-stdin

                docker push ${IMAGE_NAME}:${IMAGE_TAG}
                docker push ${IMAGE_NAME}:latest

                docker logout
            '''
        }
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
