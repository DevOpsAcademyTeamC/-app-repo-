pipeline {
    agent any
    
    stages {
        stage('Clonee Repository') {
            steps {
                // Clone the repository
                git 'https://github.com/DevOpsAcademyTeamC/-app-repo-.git'
            }
        }
        
        stage('Build Docker Image') {
            steps {
                // Build the Docker image
                script {
                    docker.build('saleor-platform:latest', '.')
                }
            }
        }
        
        stage('Run Unit Tests') {
            steps {
                // Run unit tests within the Docker container
                script {
                    def containerId = '2d42593e754c'
                    docker.image('saleor-platform:latest').run("--rm -d", "${containerId}")
                    docker.image('saleor-platform:latest').inside("${containerId}") {
                        sh 'pytest'
                    }
                }
            }
        }
        
        stage('Push Image to Docker Hub') {
            steps {
                // Push the Docker image to Docker Hub
                script {
                    docker.withRegistry('https://registry.hub.docker.com', 'dockerhub_credentials') {
                        docker.image('saleor-platform:latest').push('latest')
                    }
                }
            }
        }
    }
}
