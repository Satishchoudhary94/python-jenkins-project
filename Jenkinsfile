pipeline {
    agent any

    stages {

        stage('Checkout Code') {
            steps {
                checkout scm
                sh 'ls -l'
            }
        }

        stage('Run Tests') {
            steps {
                sh '''
                docker run --rm \
                  -v "$WORKSPACE":/app \
                  -w /app \
                  python:3.10-slim \
                  sh -c "pip install -r requirements.txt && pytest"
                '''
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t python-jenkins-app .'
            }
        }

        stage('Run Container') {
            steps {
                sh '''
                docker rm -f python-jenkins-container || true
                docker run -d -p 5002:5000 \
                  --name python-jenkins-container \
                  python-jenkins-app
                '''
            }
        }
    }
}
