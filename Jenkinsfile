pipeline {
    agent any

    environment {
        DOCKER_HUB_USERNAME = 'captcloud01'
        APP_HOME = '/usr/src/app'
        DOCKER_HUB_PASSWORD = credentials('dockerhub-credentials')
        IMAGE_NAME = "captcloud01/atechbroe"
        IMAGE_TAG = "v1"
    }

    stages {
        stage('Checkout Code') {
            steps {
                git branch: 'main', credentialsId: 'github-credentials',
                    url: 'https://github.com/shehuj/shopping_cart-java.git'
            }
        }

        stage('Build Application') {
            steps {
                sh 'mvn clean package -DskipTests'
            }
        }
        
        stage('Archive JAR') {
          steps {
            archiveArtifacts artifacts: 'target/*.jar', fingerprint: true
          }
        }

        stage('Run Tests') {
            steps {
                sh 'ls -l target'
                sh 'groups'
                sh 'ls -l /var/run/docker.sock'
                sh 'docker run hello-world'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh "docker build -t ${IMAGE_NAME}:${IMAGE_TAG} ."
            }
        }

        stage('Push Docker Image to Docker Hub') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'dockerhub-credentials',
                    usernameVariable: 'DOCKER_USERNAME',
                    passwordVariable: 'DOCKER_PASSWORD'
                )]) {
                    sh "echo \$DOCKER_PASSWORD | docker login -u \$DOCKER_USERNAME --password-stdin"
                    sh "docker push ${IMAGE_NAME}:${IMAGE_TAG}"
                }
            }
        }
    }

    post {
        success {
            echo '✅ Pipeline finished successfully!'
        }
        failure {
            echo '❌ Pipeline failed!'
        }
    }
}
