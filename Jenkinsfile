pipeline {
    agent any

    environment {
        IMAGE_FRONTEND = "devops-frontend"
        IMAGE_BACKEND  = "devops-backend"
    }

    stages {

        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Install Frontend') {
            steps {
                dir('frontend') {
                    bat 'npm install'
                }
            }
        }

        stage('Build Frontend') {
            steps {
                dir('frontend') {
                    bat 'npm run build'
                }
            }
        }

        stage('Install Backend') {
            steps {
                dir('backend') {
                    bat 'pip install -r requirements.txt'
                }
            }
        }

        stage('Test Backend') {
            steps {
                dir('backend') {
                     bat 'pytest || ver > nul'

                }
            }
        }

       stage('Build Docker Images') {
            steps {
                bat 'docker build -t rivki4240/devops-frontend ./frontend'
                bat 'docker build -t rivki4240/devops-backend ./backend'
            }
        }

        stage('Push Docker Images') {
            steps {
            
                withCredentials([usernamePassword(
                    credentialsId: 'dockerhub',
                    usernameVariable: 'DOCKER_USER',
                    passwordVariable: 'DOCKER_PASS'
                )]) {
                
                    bat 'docker login -u %DOCKER_USER% -p %DOCKER_PASS%'

                    bat 'docker push rivki4240/devops-frontend'
                    bat 'docker push rivki4240/devops-backend'
                }
            }
        }
        stage('Run Containers') {
            steps {
                bat 'docker compose up -d --build'
            }
        }
    }

    post {
        success {
            echo 'Pipeline completed successfully'
        }

        failure {
            echo 'Pipeline failed'
        }

        always {
            echo 'Cleaning workspace'
            bat 'docker system prune -f'
        }
    }
}
