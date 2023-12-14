pipeline {
    agent any
    stages {

        stage('Build') {
            steps {
                sh '''
                docker build -t stratcastor/flask-jenk:latest -t stratcastor/flask-jenk:v${BUILD_NUMBER} .
                '''
            }
        }

        stage('Push') {
            steps {
                sh '''
                docker push stratcastor/flask-jenk:latest
                docker push stratcastor/flask-jenk:v${BUILD_NUMBER}
                '''
            }
        }

        stage('Deploy') {
            steps {
                sh '''
                kubectl apply -f ./kubernetes
                kubectl rollout restart deployment flask-deployment
                kubectl rollout restart deployment nginx-deployment
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