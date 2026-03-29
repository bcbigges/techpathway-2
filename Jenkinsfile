pipeline {
    agent any

    environment {
        DOCKERHUB_USER  = "mrbiggc"
        BACKEND_IMAGE   = "${DOCKERHUB_USER}/techpathway-backend:${BUILD_NUMBER}"
        FRONTEND_IMAGE  = "${DOCKERHUB_USER}/techpathway-frontend:${BUILD_NUMBER}"
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

        stage('Push to Docker Hub') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub-creds', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                    sh '''
                        echo "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin
                        docker push ''' + env.BACKEND_IMAGE + '''
                        docker push ''' + env.FRONTEND_IMAGE + '''
                        docker logout
                    '''
                }
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
