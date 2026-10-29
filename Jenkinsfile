pipeline {
    agent any

    environment {
        DOCKERHUB_CREDENTIALS = credentials('dockerhub-credentials')
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/hiraahmadcui/banking-app.git'
            }
        }
        
        stage('Build and Deploy') {
            steps {
                // Login to Docker Hub
                sh 'echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin'
                
                // Build the image
                sh 'docker build -t hiracui/fastapi-app:latest .'
                
                // Stop old containers and start new ones using the Jenkins-specific compose file
                sh 'docker compose -f docker-compose-jenkins.yml down || true'
                sh 'docker compose -f docker-compose-jenkins.yml up -d'
            }
        }
    }

    post {
        success {
            emailext(
                subject: "Build SUCCESS: ${env.JOB_NAME} #${env.BUILD_NUMBER}",
                body: "The build for ${env.JOB_NAME} (Build #${env.BUILD_NUMBER}) was SUCCESSFUL.\n\nTriggered by: ${env.CAUSE}\n\nCheck console output at: ${env.BUILD_URL}",
                to: '${CHANGE_AUTHOR_EMAIL}'
            )
        }
        failure {
            emailext(
                subject: "Build FAILURE: ${env.JOB_NAME} #${env.BUILD_NUMBER}",
                body: "The build for ${env.JOB_NAME} (Build #${env.BUILD_NUMBER}) FAILED.\n\nTriggered by: ${env.CAUSE}\n\nCheck console output at: ${env.BUILD_URL}",
                to: '${CHANGE_AUTHOR_EMAIL}'
            )
        }
    }
}
