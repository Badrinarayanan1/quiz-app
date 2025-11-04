pipeline {
    agent any

    environment {
        DOCKERHUB_CREDENTIALS = 'dockerhub-creds'
        AWS_CREDENTIALS = 'aws-creds'
        BACKEND_IMAGE = 'badrinarayanan1/quizroom-backend'
        FRONTEND_IMAGE = 'badrinarayanan1/quizroom-frontend'
        REGION = 'us-east-1'
        CLUSTER_NAME = 'quizroom-cluster'
    }

    stages {
        stage('Checkout Code') {
            steps {
                git branch: 'main', 
                    url: 'https://github.com/Badrinarayanan1/quiz-app.git',
                    credentialsId: 'github-creds'

            }
        }

        stage('Build Docker Images') {
            steps {
                sh 'docker build -t $BACKEND_IMAGE:latest ./backend'
                sh 'docker build -t $FRONTEND_IMAGE:latest ./frontend'
            }
        }

        stage('Push to DockerHub') {
            steps {
                withCredentials([usernamePassword(credentialsId: DOCKERHUB_CREDENTIALS, usernameVariable: 'USER', passwordVariable: 'PASS')]) {
                    sh '''
                    echo $PASS | docker login -u $USER --password-stdin
                    docker push $BACKEND_IMAGE:latest
                    docker push $FRONTEND_IMAGE:latest
                    '''
                }
            }
        }

        stage('Deploy to EKS') {
            steps {
                withCredentials([[$class: 'AmazonWebServicesCredentialsBinding', credentialsId: AWS_CREDENTIALS]]) {
                    sh '''
                    aws eks update-kubeconfig --region $REGION --name $CLUSTER_NAME
                    kubectl set image deployment/quizroom-backend quizroom-backend=$BACKEND_IMAGE:latest --record || kubectl apply -f k8s/backend-deployment.yaml
                    kubectl set image deployment/quizroom-frontend quizroom-frontend=$FRONTEND_IMAGE:latest --record || kubectl apply -f k8s/frontend-deployment.yaml
                    '''
                }
            }
        }
    }
}
