Basic Pipeline :

 pulling docker image 

pipeline {
    agent any

    environment {
        IMAGE_NAME = "yreddys211/dockerdemo"
        IMAGE_TAG = "latest"
        CONTAINER_NAME = "dockerdemo-app"
    }

    stages {
        stage('Pull Image from Docker Hub') {
            steps {
                sh "docker pull ${IMAGE_NAME}:${IMAGE_TAG}"
            }
        }

        stage('Run Container') {
            steps {
                // Optional: Stop and remove if already running
                sh "docker rm -f ${CONTAINER_NAME} || true"
                
                // Run the new container
                sh "docker run -d --name ${CONTAINER_NAME} -p 8082:8080 ${IMAGE_NAME}:${IMAGE_TAG}"
            }
        }
    }
}

