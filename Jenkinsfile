pipeline {
    agent any

    environment {
        DOCKER_IMAGE = "varun1411/java-cicd-demo:latest"
        KUBECONFIG_PATH = "${WORKSPACE}/kubeconfig"
    }

    stages {
        stage('Checkout') {
            steps {
                git credentialsId: '2c8160d2-6d8b-4a43-8937-6bf5dbbd7925',
                    url: 'https://github.com/varun14112000/java-cicd-demo.git',
                    branch: 'main'
            }
        }

        stage('Build with Maven') {
            steps {
                sh 'mvn clean package'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t $DOCKER_IMAGE .'
            }
        }

        stage('Push Docker Image') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub-cred', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                    sh '''
                        echo "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin
                        docker push $DOCKER_IMAGE
                    '''
                }
            }
        }

        stage('Configure Kubeconfig') {
            steps {
                withCredentials([aws(credentialsId: 'aws-cred', accessKeyVariable: 'AWS_ACCESS_KEY_ID', secretKeyVariable: 'AWS_SECRET_ACCESS_KEY')]) {
                    sh '''
                        mkdir -p $(dirname $KUBECONFIG_PATH)
                        aws eks update-kubeconfig --region us-east-1 --name my-eks-cluster --kubeconfig $KUBECONFIG_PATH
                    '''
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                sh '''
                    kubectl --kubeconfig=$KUBECONFIG_PATH apply -f k8s/deployment.yaml
                    kubectl --kubeconfig=$KUBECONFIG_PATH apply -f k8s/service.yaml
                '''
            }
        }
    }
}



