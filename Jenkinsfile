pipeline {
    agent any

    stages {

        stage('Build Backend Image') {
            steps {
                sh 'docker build -t backend-app backend'
            }
        }

        stage('Deploy Backends') {
            steps {
                sh '''
                docker rm -f backend1 backend2 nginx-lb || true

                docker run -d --name backend1 -p 8081:8080 backend-app
                docker run -d --name backend2 -p 8082:8080 backend-app

                sleep 3
                '''
            }
        }

        stage('Deploy NGINX Load Balancer') {
            steps {
                sh '''
                docker run -d --name nginx-lb \
                -p 80:80 \
                --link backend1 \
                --link backend2 \
                nginx

                sleep 2

                docker cp nginx/default.conf nginx-lb:/etc/nginx/conf.d/default.conf

                docker exec nginx-lb nginx -s reload
                '''
            }
        }

    }
}