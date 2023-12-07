pipeline {
    agent any
    stages {

        stage('Init') {
            steps {
                sh '''
                ssh -i ~/.ssh/id_rsa jenkins@10.154.0.20 << EOF
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
                docker build -t stratcastor/flask-jenk:latest -t stratcastor/flask-jenk:v${BUILD_NUMBER} .
                docker build -t stratcastor/nginx-jenk:latest -t stratcastor/nginx-jenk:v${BUILD_NUMBER} ./nginx
                '''
            }
        }

        stage('Push') {
            steps {
                sh '''
                docker push stratcastor/flask-jenk:latest
                docker push stratcastor/flask-jenk:v${BUILD_NUMBER}
                docker push stratcastor/nginx-jenk:latest
                docker push stratcastor/nginx-jenk:v${BUILD_NUMBER}
                '''
            }
        }

        stage('Deploy') {
            steps {
                sh '''
                ssh -i ~/.ssh/id_rsa jenkins@10.154.0.20 << EOF
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