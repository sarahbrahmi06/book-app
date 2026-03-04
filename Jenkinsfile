pipeline {
    agent any
    environment {
        IMAGE_NAME = 'book-app'
        EXT_PORT   = '3000'
    }
    stages {
        stage('Build') {
            agent {
                docker {
                    image 'node:18-alpine'
                    args '-u root --entrypoint=""'
                    reuseNode true
                }
            }
            steps {
                echo 'Installing dependencies...'
                sh 'npm install'
            }
        }
        stage('Test') {
            agent {
                docker {
                    image 'node:18-alpine'
                    args '-u root --entrypoint=""'
                    reuseNode true
                }
            }
            steps {
                echo 'Running tests...'
                sh 'npm test'
            }
            post {
                success {
                    echo 'Tests passed successfully!'
                }
                failure {
                    echo 'Tests failed! Check the logs.'
                }
            }
        }
        stage('Build Docker Image') {
            steps {
                echo 'Building Docker image...'
                sh "docker build -t ${IMAGE_NAME} ."
            }
        }
        stage('Deploy') {
            steps {
                echo 'Cleaning old container...'
                sh "docker stop ${IMAGE_NAME}  true"
                sh "docker rm ${IMAGE_NAME}  true"
                echo 'Launching new container...'
                sh "docker run -d --name ${IMAGE_NAME} -p ${EXT_PORT}:3000 ${IMAGE_NAME}"
                echo "App is running at http://localhost:${EXT_PORT}"
            }
        }
    }
    post {
        always {
            echo 'Pipeline finished!'
        }
        success {
            echo 'Pipeline completed without errors!'
        }
        failure {
            echo 'Pipeline encountered errors!'
        }
    }
}
