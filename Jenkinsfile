pipeline {
    agent any
    stages {
        stage('Build') {
            steps {
                sh '''
                docker build -t andymac011171/duo-deploy-flask:latest -t andymac011171/duo-deploy-flask:v${BUILD_NUMBER} .
                '''
            }
        }

        stage('Push') {
            steps {
                sh '''
                docker push andymac011171/duo-deploy-flask:latest
                docker push andymac011171/duo-deploy-flask:v${BUILD_NUMBER}
                '''
            }
        }

        stage('Deploy') {
            steps {
                sh '''
                kubectl apply -f ./Kubernetes
                kubectl set image deployment/flask-deployment task1=andymac011171/duo-deploy-flask:v${BUILD_NUMBER}
                '''
            }
        }

        stage('CleanUp') {
            steps {
                sh '''
                docker system prune -f 
                '''
            }
        }
    }

}