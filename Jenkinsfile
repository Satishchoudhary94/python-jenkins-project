pipeline {
    agent any

    environment {
        IMAGE_NAME = "python-jenkins-app"
    }

    stages {

        stage('Checkout Code') {
            steps {
                git branch: 'main',
                    credentialsId: 'github-creds',
                    url: 'https://github.com/Satishchoudhary94/python-jenkins-project.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh '''
                  docker build -t python-jenkins-app:latest .
                '''
            }
        }

        stage('Deploy DEV') {
            steps {
                sh '''
                  docker rm -f python-app-dev || true
                  docker run -d -p 5002:5000 \
                    --name python-app-dev \
                    python-jenkins-app:latest
                '''
            }
        }

        stage('Approve PROD Deployment') {
            steps {
                input message: 'Deploy to PRODUCTION?', ok: 'YES, DEPLOY'
            }
        }

        stage('Deploy PROD') {
            steps {
                sh '''
                  docker rm -f python-app-prod || true
                  docker run -d -p 5004:5000 \
                    --name python-app-prod \
                    python-jenkins-app:latest
                '''
            }
        }
    }

    post {

        success {
            emailext(
                subject: "✅ SUCCESS | ${JOB_NAME} #${BUILD_NUMBER}",
                body: """
Build SUCCESS ✅

Job: ${JOB_NAME}
Build: ${BUILD_NUMBER}

DEV  : http://localhost:5002
PROD : http://localhost:5004

Logs:
${BUILD_URL}
""",
                to: "Satishchaudhary877@gmail.com",
                from: "jenkins@localhost",
                replyTo: "Satishchaudhary877@gmail.com"
            )
        }

        failure {
            emailext(
                subject: "❌ FAILED | ${JOB_NAME} #${BUILD_NUMBER}",
                body: """
Build FAILED ❌

Job: ${JOB_NAME}
Build: ${BUILD_NUMBER}

Logs:
${BUILD_URL}
""",
                to: "Satishchaudhary877@gmail.com",
                from: "jenkins@localhost",
                replyTo: "Satishchaudhary877@gmail.com"
            )
        }
    }
}
