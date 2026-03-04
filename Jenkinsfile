pipeline {
    agent any

    environment {
        REPO_URL   = 'https://github.com/malkiAbdelhamid/book-app'
        BRANCH     = 'master'
        IMAGE_NAME = 'book-app'
        EXT_PORT   = '3000'
    }

    stages {

        stage('Clone') {
            steps {
                echo 'Cloning the repository...'
                sh 'rm -rf ./* ./.git || true'
                sh "git clone -b ${BRANCH} ${REPO_URL} ."
            }
        }

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
                success { echo 'Tests passed!' }
                failure { echo 'Tests failed!' }
            }
        }

        stage('Build Docker Image') {
            steps {
                sh "docker build -t ${IMAGE_NAME} ."
            }
        }

        stage('Approval') {
            steps {
                input message: 'Deploy to production? Approve or Abort.', ok: 'Approve'
            }
        }

        stage('Deploy') {
            steps {
                sh "docker stop ${IMAGE_NAME} || true"
                sh "docker rm ${IMAGE_NAME} || true"
                sh "docker run -d --name ${IMAGE_NAME} -p ${EXT_PORT}:3000 ${IMAGE_NAME}"
                echo "App running at http://localhost:${EXT_PORT}"
            }
        }
    }

    post {
        always { echo 'Pipeline finished!' }
        success { echo 'Pipeline completed without errors!' }
        failure { echo 'Pipeline encountered errors!' }
    }
}
