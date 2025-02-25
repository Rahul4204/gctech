pipeline {
    agent any

    stages {
        stage('Clone Repository') {
            steps {
                git branch: 'main', url: 'https://github.com/Rahul4204/gctech.git'
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
                docker build -t 8008150141/myapp .
                """
            }
        }

        stage('Push Docker Image to Docker Hub') {
            steps {
                withDockerRegistry([credentialsId: 'docker-hub-credentials', url: 'https://index.docker.io/v1/']) {
                    sh """
                    docker push 8008150141/myapp
                    docker stop 8008150141/myapp ||true 
                    docker rm -f 8008150141/myapp
                    docker rmi 8008150141/myapp
                    docker pull 8008150141/myapp
                    docker run -d -p 8082:8080 8008150141/myapp
                    """
                    
                }
            }
        }
    }
}
