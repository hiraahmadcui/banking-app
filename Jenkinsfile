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
                sh 'echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin'
                sh 'docker build -t hiracui/fastapi-app:latest .'
                sh 'docker compose -f docker-compose-jenkins.yml down || true'
                sh 'docker compose -f docker-compose-jenkins.yml up -d'
            }
        }
    }

    post {
        success {
            emailext (
                subject: "Build SUCCESS: ${env.JOB_NAME} #${env.BUILD_NUMBER}",
                body: "The build was SUCCESSFUL.",
                to: "${env.CHANGE_AUTHOR_EMAIL}, ${env.GIT_COMMITTER_EMAIL}"
            )
        }
        failure {
            emailext (
                subject: "Build FAILURE: ${env.JOB_NAME} #${env.BUILD_NUMBER}",
                body: "The build FAILED.",
                to: "${env.CHANGE_AUTHOR_EMAIL}, ${env.GIT_COMMITTER_EMAIL}"
            )
        }
    }
}
