pipeline {
    agent any

    environment {
        DOCKERHUB_CREDENTIALS = credentials('dockerhub')
        EC2_IP = "13.126.47.0"
        FRONTEND_IMAGE = "ashfak9673/frontend-app:latest"
        BACKEND_IMAGE = "ashfak9673/backend-app:latest"
    }

    stages {
        stage('Clone Repository') {
            steps {
                git branch: 'main', url: 'https://github.com/ashfakshaikh96/crud-app-repo.git'
            }
        }

        stage('Build Backend Image') {
            steps {
                sh """
                cd backend
                docker build -t ${BACKEND_IMAGE} .
                """
            }
        }

        stage('Build Frontend Image') {
            steps {
                sh """
                cd frontend
                docker build -t ${FRONTEND_IMAGE} .
                """
            }
        }

        stage('Push Images to DockerHub') {
            steps {
                sh """
                echo ${DOCKERHUB_CREDENTIALS_PSW} | docker login -u ${DOCKERHUB_CREDENTIALS_USR} --password-stdin
                docker push ${BACKEND_IMAGE}
                docker push ${FRONTEND_IMAGE}
                """
            }
        }

        stage('Deploy on EC2') {
            steps {
                sshagent(['ec2key']) {
                    sh """
                        ssh -o StrictHostKeyChecking=no ubuntu@${EC2_IP} '
                            cd crud-dd-task-mean-app
                            docker compose pull
                            docker compose down
                            docker compose up -d
                        '
                    """
                }
            }
        }
    }
}
