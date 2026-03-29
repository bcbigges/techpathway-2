pipeline {
    agent any

    environment {
        BACKEND_IMAGE  = "techpathway-backend:${BUILD_NUMBER}"
        FRONTEND_IMAGE = "techpathway-frontend:${BUILD_NUMBER}"
    }

    stages {
        stage('Build Backend') {
            steps {
                sh "docker build -t ${BACKEND_IMAGE} ./backend"
            }
        }

        stage('Build Frontend') {
            steps {
                sh "docker build -t ${FRONTEND_IMAGE} ./frontend"
            }
        }

        stage('Test Backend') {
            steps {
                sh """
                    docker run -d --name backend-test-${BUILD_NUMBER} ${BACKEND_IMAGE}
                    sleep 5
                    docker exec backend-test-${BUILD_NUMBER} wget -q -O- http://localhost:8080 && echo 'Backend OK'
                    docker stop backend-test-${BUILD_NUMBER}
                """
            }
        }

        stage('Test Frontend') {
            steps {
                sh """
                    docker run -d --name frontend-test-${BUILD_NUMBER} ${FRONTEND_IMAGE}
                    sleep 5
                    docker ps --filter name=frontend-test-${BUILD_NUMBER} --filter status=running | grep frontend-test-${BUILD_NUMBER} && echo 'Frontend OK'
                    docker stop frontend-test-${BUILD_NUMBER}
                """
            }
        }
    }

    post {
        always {
            sh "docker stop backend-test-${BUILD_NUMBER} || true"
            sh "docker rm backend-test-${BUILD_NUMBER} || true"
            sh "docker stop frontend-test-${BUILD_NUMBER} || true"
            sh "docker rm frontend-test-${BUILD_NUMBER} || true"
            sh "docker rmi ${BACKEND_IMAGE} || true"
            sh "docker rmi ${FRONTEND_IMAGE} || true"
        }
    }
}
