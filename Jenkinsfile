pipeline {
    agent any
    environment {
        OLD_VERSION ='v1'
        VERSION = "v2"
        DOCKERHUB_CREDS = credentials('dockerhub')
    }
    stages {
        stage('Build Images') {
            steps {
                sh "docker build -t ${DOCKERHUB_CREDS_USR}/flask-app:${VERSION}"
                sh "docker build -t ${DOCKERHUB_CREDS_USR}/nginx:${VERSION} -f Dockerfile.nginx"
                // Docker build flask and nginx apps.
            }
        }
        stage('Deploy Containers') {
            steps {
                sh "docker network create task1-net || true"
                sn "docker rm -f \$(docker ps -aq) || true"
                sh "docker run -d --network task1-net --name flask-app -e YOUR_NAME=Jenkins ${DOCKERHUB_CREDS_USR}/task1-flask-app:${VERSION}"
                sh "docker run -d -p 80:80 --name nginx --network task1-net ${DOCKERHUB_CREDS_USR}/task1-nginx:${VERSION}"
                // docker create network, remove previous docker runs, and run new ones.
            }
        }
        stage('Push to DockerHub') {
            steps {
                sh "docker login -u ${DOCKERHUB_CREDS_USR} -p ${DOCKERHUB_CREDS_PSW}"
                sh "docker push ${DOCKERHUB_CREDS_USR}/Task1-flask-app:${VERSION}"
                sh "docker push ${DOCKERHUB_CREDS_USR}/Task1-nginx:${VERSION}"
                sh "docker logout"

                //
            }
        }
    }
    post {
        always {
            cleanWs()
            sh "docker rmi ${DOCKERHUB_CREDS_USR}/task1-flask-app:${VERSION} || true"
            sh "docker rmi ${DOCKERHUB_CREDS_USR}/task1-nginx:${VERSION} || true"
        }
    }
}