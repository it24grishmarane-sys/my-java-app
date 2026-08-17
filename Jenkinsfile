pipeline {
    agent any

    environment {
        APP_NAME         = 'my-java-app'
        REGISTRY         = 'docker.io'
        REGISTRY_CRED_ID = 'docker-hub-credentials'
        IMAGE_TAG        = "${BUILD_NUMBER}"
        GRADLE_OPTS      = '-Dorg.gradle.internal.http.connectionTimeout=120000 -Dorg.gradle.internal.http.socketTimeout=120000'
    }

    stages {
        stage('Build & Test') {
            steps {
                sh 'chmod +x gradlew'
                sh './gradlew clean build --no-daemon'
            }
            post {
                always {
                    junit allowEmptyResults: true, testResults: '**/build/test-results/test/*.xml'
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