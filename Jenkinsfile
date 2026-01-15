pipeline {
    agent any

    environment {
        IMAGE_NAME = "python-jenkins-app"
        IMAGE_TAG  = "${BUILD_NUMBER}"
    }

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
                sh """
                  docker build -t ${IMAGE_NAME}:${IMAGE_TAG} .
                  docker tag ${IMAGE_NAME}:${IMAGE_TAG} ${IMAGE_NAME}:latest
                """
            }
        }

        stage('Deploy DEV') {
            steps {
                sh '''
                  docker stop python-app-dev || true
                  docker rm -f python-app-dev || true

                  docker run -d -p 5002:5000 \
                    --name python-app-dev \
                    python-jenkins-app:latest
                '''
            }
        }

        stage('Deploy STAGING') {
            steps {
                sh '''
                  docker stop python-app-staging || true
                  docker rm -f python-app-staging || true

                  docker run -d -p 5003:5000 \
                    --name python-app-staging \
                    python-jenkins-app:latest
                '''
            }
        }

        stage('Approve PROD Deployment') {
            when {
                branch 'main'
            }
            steps {
                input message: 'Deploy to PRODUCTION?', ok: 'YES, DEPLOY'
            }
        }

        stage('Deploy PROD') {
            steps {
                sh '''
                  docker stop python-app-prod || true
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
                mimeType: 'text/html',
                body: """
                <h2 style="color:green;">Build SUCCESS</h2>
                <b>Job:</b> ${JOB_NAME}<br>
                <b>Build:</b> ${BUILD_NUMBER}<br><br>

                DEV → http://localhost:5002<br>
                STAGING → http://localhost:5003<br>
                PROD → http://localhost:5004<br><br>

                <a href="${BUILD_URL}">View Logs</a>
                """,
                to: "satishchaudhary877@gmail.com"
            )
        }

        failure {
            emailext(
                subject: "❌ xFAILED | ${JOB_NAME} #${BUILD_NUMBER}",
                mimeType: 'text/html',
                body: """
                <h2 style="color:red;">Build FAILED</h2>
                <b>Job:</b> ${JOB_NAME}<br>
                <b>Build:</b> ${BUILD_NUMBER}<br><br>

                <a href="${BUILD_URL}">View Logs</a>
                """,
                to: "satishchaudhary877@gmail.com"
            )
        }
    }
}
