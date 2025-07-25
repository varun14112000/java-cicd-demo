pipeline {
    agent any

    environment {
        DOCKER_HUB_USERNAME = 'varun1411' // Your Docker Hub username
    }

    stages {
        stage('Checkout') {
            steps {
                git credentialsId: '2c8160d2-6d8b-4a43-8937-6bf5dbbd7925', url: 'https://github.com/varun14112000/java-cicd-demo.git', branch: 'main'
            }
        }

        stage('Build with Maven') {
            steps {
                sh 'mvn clean package'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh "docker build -t ${DOCKER_HUB_USERNAME}/java-cicd-demo:latest ."
            }
        }

        stage('Push Docker Image') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub-credentials-id', usernameVariable: 'USERNAME', passwordVariable: 'PASSWORD')]) {
                    sh """
                        echo $PASSWORD | docker login -u $USERNAME --password-stdin
                        docker push ${DOCKER_HUB_USERNAME}/java-cicd-demo:latest
                    """
                }
            }
        }

        stage('Deploy to Kubernetes') {
            when {
                expression { currentBuild.currentResult == 'SUCCESS' }
            }
            steps {
                sh 'kubectl apply -f k8s/deployment.yaml'
            }
        }
    }
}


