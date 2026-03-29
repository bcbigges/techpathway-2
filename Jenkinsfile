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
                sh "docker run --rm ${BACKEND_IMAGE} node -e \"require('./index')\""
            }
        }

        stage('Test Frontend') {
            steps {
                sh "docker run --rm ${FRONTEND_IMAGE} npm run build"
            }
        }
    }

    post {
        always {
            sh "docker rmi ${BACKEND_IMAGE} || true"
            sh "docker rmi ${FRONTEND_IMAGE} || true"
        }
    }
}
