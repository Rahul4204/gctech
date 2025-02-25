pipeline {
    agent any

    environment {
        
        USERNAME = '8008150141'
        IMAGENAme = 'gctech-app'
        REPO_URL = 'https://github.com/Rahul4204/gctech.git' 
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
                docker build -t ${USERNAME}/${IMAGE} .
                """
            }
        }

        stage('Push Docker Image to Docker Hub') {
            steps {
                withDockerRegistry([credentialsId: 'docker-hub-credentials', url: 'https://index.docker.io/v1/']) {
                    sh """
                    docker push ${USERNAME}/${IMAGE}
                    docker stop ${USERNAME}/${IMAGE} ||true 
                    docker rm -f ${USERNAME}/${IMAGE}
                    docker rmi ${USERNAME}/${IMAGE}
                    docker pull ${USERNAME}/${IMAGE}
                    docker run -d -p 8082:8080 ${USERNAME}/${IMAGE}
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
