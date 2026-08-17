pipeline {
    agent any

    environment {
        APP_NAME         = 'my-java-app'
        REGISTRY         = 'docker.io'
        REGISTRY_CRED_ID = 'docker-hub-credentials'
        IMAGE_TAG        = "${BUILD_NUMBER}"
    }

    stages {
        stage('Build & Test') {
            steps {
                sh './gradlew clean build'
            }
            post {
                always {
                    junit '**/build/test-results/test/*.xml'
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    dockerImage = docker.build("${REGISTRY}/${APP_NAME}:${IMAGE_TAG}", ".")
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                script {
                    docker.withRegistry("https://${REGISTRY}", "${REGISTRY_CRED_ID}") {
                        dockerImage.push()
                    }
                }
            }
        }
    }
}