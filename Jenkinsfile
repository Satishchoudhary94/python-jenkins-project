pipeline {
    agent any

    stages {

        stage('Checkout Code') {
            steps {
                git branch: 'main',
                    credentialsId: 'github-creds',
                    url: 'https://github.com/Satishchoudhary94/python-jenkins-project.git'
                sh 'ls -l'
            }
        }


        stage('Run Tests') {
            steps {
                script {
                    docker.image('python:3.10-slim').inside(
                        "--user root -v ${env.WORKSPACE}:/app -w /app"
                    ) {
                        sh '''
                          pip install -r requirements.txt
                          pytest
                        '''
                    }
                }
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
