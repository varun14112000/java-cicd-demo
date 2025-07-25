pipeline {
    agent any

    environment {
        IMAGE_NAME = 'varun1411/java-cicd-demo:latest'
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', credentialsId: '2c8160d2-6d8b-4a43-8937-6bf5dbbd7925', url: 'https://github.com/varun14112000/java-cicd-demo.git'
            }
        }

        stage('Build with Maven') {
            steps {
                sh 'mvn clean package'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh "docker build -t ${IMAGE_NAME} ."
            }
        }

        stage('Push Docker Image') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub-cred', usernameVariable: 'USERNAME', passwordVariable: 'PASSWORD')]) {
                    sh '''
                        echo $PASSWORD | docker login -u $USERNAME --password-stdin
                        docker push ${IMAGE_NAME}
                    '''
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                withCredentials([file(credentialsId: 'kubeconfig-cred', variable: 'KUBECONFIG')]) {
                    sh '''
                        kubectl config get-contexts
                        kubectl config use-context your-cluster-context
                        kubectl apply -f k8s-deployment.yaml
                    '''
                }
            }
        }
    }
}


