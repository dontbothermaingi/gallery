pipeline {
    agent any
    tools {
        nodejs 'NodeJS' // Ensure NodeJS is configured in Jenkins
    }

    environment {
        SLACK_CHANNEL = '#denis_ip1'  // The Slack channel to notify
    }

    stages {
        stage('Clone repository') {
            steps {
                echo 'Cloning repository'
                git 'https://github.com/dontbothermaingi/gallery.git'
            }
        }

        stage('Build') {
            steps {
                echo 'Installing Dependencies'
                sh 'nvm use 21 || true'  // Use nvm if installed, but don't fail if not available
                sh 'npm install'  // Faster and more reliable for CI environments
            }
        }

        stage('Switch branch') {
            steps {
                echo 'Switching to test branch'
                sh 'git checkout test'
            }
        }

        stage('Test') {
            steps {
                echo 'Running tests'
                sh 'npm test'
            }
        }
    }

    post {
        success {
            script {
                // Use Slack credentials from Jenkins credentials store
                withCredentials([string(credentialsId: 'SLACK_CREDENTIALS', variable: 'SLACK_TOKEN')]) {
                    slackSend(
                        channel: "${SLACK_CHANNEL}",
                        token: "${SLACK_TOKEN}",
                        message: "Build ${env.BUILD_ID} was successfully deployed!"
                    )
                }
            }
        }

        failure {
            script {
                // Use Slack credentials from Jenkins credentials store
                withCredentials([string(credentialsId: 'SLACK_CREDENTIALS', variable: 'SLACK_TOKEN')]) {
                    slackSend(
                        channel: "${SLACK_CHANNEL}",
                        token: "${SLACK_TOKEN}",
                        message: "Build ${env.BUILD_ID} failed."
                    )
                }
            }
        }
    }
}
