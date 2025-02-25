pipeline {
    agent any

    environment {
        
        DOCKER_HUB_USERNAME = '8008150141'
        DOCKER_IMAGE = 'gctech-app'
        DOCKER_TAG = 'latest'
        REPO_URL = 'https://github.com/Rahul4204/gctech.git' 
        
        EC2_HOST = 'ec2-user@172.31.11.78'
    }

    stages {
        stage('Clone Repository') {
            steps {
                git branch: 'main', url: "${REPO_URL}"
            }
        }

        stage('Build Java Project') {
            steps {
                sh 'mvn clean package'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh """
                docker build -t ${DOCKER_HUB_USERNAME}/${DOCKER_IMAGE}:${DOCKER_TAG} .
                """
            }
        }

        stage('Push Docker Image to Docker Hub') {
            steps {
                withDockerRegistry([credentialsId: 'docker-hub-credentials', url: 'https://index.docker.io/v1/']) {
                    sh """
                    docker push ${DOCKER_HUB_USERNAME}/${DOCKER_IMAGE}:${DOCKER_TAG}
                    docker stop ${DOCKER_HUB_USERNAME}/${DOCKER_IMAGE}:${DOCKER_TAG} ||true && docker rm -f ${DOCKER_HUB_USERNAME}/${DOCKER_IMAGE}:${DOCKER_TAG}
                    docker pull ${DOCKER_HUB_USERNAME}/${DOCKER_IMAGE}:${DOCKER_TAG}
                    docker run -d -p 8082:8080 ${DOCKER_HUB_USERNAME}/${DOCKER_IMAGE}:${DOCKER_TAG}
                    """
                    
                }
            }
        }
    }

    triggers {
        pollSCM('H/5 * * * *')
    }

    post {
        success {
            echo "Pipeline executed successfully and the application is deployed!"
        }
        failure {
            echo "Pipeline execution failed. Please check the logs."
        }
    }
}
