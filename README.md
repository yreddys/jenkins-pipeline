Basic Pipeline :

 pulling docker image 

```groovy
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


Pipeline 2 :
git clone -> docker image -> docker run 

```groovy

pipeline {
    agent any

    environment {
        REPO_URL = "https://github.com/yreddys/DockerForDeployment.git"
        IMAGE_NAME = "dockerdemo"
        IMAGE_TAG = "latest"
        CONTAINER_NAME = "dockerdemo-app"
    }

    stages {
        stage('Clone Repository') {
            steps {
                // Clean previous repo if exists
                sh 'rm -rf DockerForDeployment || true'
                sh "git clone ${REPO_URL}"
            }
        }

        stage('Build JAR using Maven') {
            steps {
                dir('DockerForDeployment') {
                    sh 'mvn clean package -DskipTests'
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                dir('DockerForDeployment') {
                    sh "docker build -t ${IMAGE_NAME}:${IMAGE_TAG} ."
                }
            }
        }

        stage('Run Docker Container') {
            steps {
                // Stop and remove if running
                sh "docker rm -f ${CONTAINER_NAME} || true"

                // Run fresh container
                sh "docker run -d --name ${CONTAINER_NAME} -p 8082:8082 ${IMAGE_NAME}:${IMAGE_TAG}"
            }
        }
    }
}
