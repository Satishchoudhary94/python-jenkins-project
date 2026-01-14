pipeline {
    agent any

    stages {

        stage('Clone Code') {
            steps {
                echo 'Code cloned from GitHub'
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
                docker run -d -p 5002:5000 --name python-jenkins-container python-jenkins-app
                '''
            }
        }
    }
}
