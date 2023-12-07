pipeline {
    agent any
    stages {

        stage('Init') {
            steps {
                sh '''
                docker network create jenk-network || echo "Network Already Exists"
                docker stop flask-app || echo "flask-app Not Running"
                docker stop nginx || echo "nginx Not Running"
                docker rm flask-app || echo "flask-app Not Running"
                docker rm nginx || echo "nginx Not Running"
                '''
            }
        }

        stage('Build') {
            steps {
                sh '''
                docker build -t stratcastor/flask-jenk .
                docker build -t stratcastor/nginx-jenk ./nginx
                '''
            }
        }

        stage('Push') {
            steps {
                sh '''
                docker push stratcastor/flask-jenk 
                docker push stratcastor/nginx-jenk
                '''
            }
        }

        stage('Deploy') {
            steps {
                sh '''
                docker run -d --name flask-app --network jenk-network stratcastor/flask-jenk
                docker run -d -p 80:80 --name nginx --network jenk-network stratcastor/nginx-jenk
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