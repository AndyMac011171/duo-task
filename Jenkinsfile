pipeline {
    agent any
    stages {
        stage('Init') {
            steps {
                script {
                    if (env.GIT_BRANCH == "origin/master") {
                        sh '''
                        kubectl create namespace prod || echo "Namespace prod already exists"
                        '''
                    } else if (env.GIT_BRANCH == "origin/dev") {
                        sh '''
                        kubectl create namespace dev || echo "Namespace dev already exists"
                        '''
                    } else {
                        sh '''
                        echo "Branch not recognised"
                        '''
                    }
                }
            }
        }
        stage('Build') {
            steps {
                script {
                    if (env.GIT_BRANCH == "origin/master") {
                        sh '''
                        docker build -t andymac011171/duo-deploy-flask:latest -t andymac011171/duo-deploy-flask:prod-v${BUILD_NUMBER} .
                        '''
                    } else if (env.GIT_BRANCH == "origin/dev") {
                        sh '''
                        docker build -t andymac011171/duo-deploy-flask:latest -t andymac011171/duo-deploy-flask:dev-v${BUILD_NUMBER} .
                        '''
                    } else {
                        sh '''
                        echo "Branch not recognised"
                        '''
                    }
                }
            }
        }
        stage('Push') {
            steps {
                script {
                    if (env.GIT_BRANCH == "origin/master") {
                        sh '''
                        docker push andymac011171/duo-deploy-flask:latest
                        docker push andymac011171/duo-deploy-flask:prod-v${BUILD_NUMBER}
                        '''
                    } else if (env.GIT_BRANCH == "origin/dev") {
                        sh '''
                        docker push andymac011171/duo-deploy-flask:latest
                        docker push andymac011171/duo-deploy-flask:dev-v${BUILD_NUMBER}
                        '''
                    } else {
                        sh '''
                        echo "Branch not recognised"
                        '''
                    }
                }
            }
        }

        stage('Deploy') {
            steps {
                script {
                    if (env.GIT_BRANCH == "origin/master") {
                        sh '''
                        kubectl apply -n prod -f ./Kubernetes
                        kubectl set image deployment/flask-container task1=andymac011171/duo-deploy-flask:prod-v${BUILD_NUMBER} -n prod
                        '''
                    } else if (env.GIT_BRANCH == "origin/dev") {
                        sh '''
                        kubectl apply -n dev -f ./Kubernetes
                        kubectl set image deployment/flask-container task1=andymac011171/duo-deploy-flask:dev-v${BUILD_NUMBER} -n dev
                        '''
                    } else {
                        sh '''
                        echo "Branch not recognised"
                        '''
                    }
                }
                
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