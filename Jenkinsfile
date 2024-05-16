pipeline {
    agent any
    
    environment {
        DOCKER_HOST = 'unix:///home/jakub-marzewski/.docker/desktop/docker-cli.sock'
    }
    
    stages {
        stage('Clone Repository') {
            steps {
                checkout([$class: 'GitSCM', 
                          branches: [[name: '*/main']],
                          userRemoteConfigs: [[url: 'https://github.com/DevOpsAcademyTeamC/-app-repo-.git']]])
            }
        }
        
        stage('Build Docker Images with Docker Compose') {
            steps {
                sh 'docker-compose build'
            }
        }
        
        stage('Run Unit Tests') {
            steps {
                script {
                    // Start the Docker container for the API service
                    def containerId = sh(script: 'docker-compose ps -q api', returnStdout: true).trim()
                    // Get into the Docker container and run commands interactively
                    sh "docker exec -i ${containerId} bash -c 'pytest'"
                    // Stop the Docker container after running tests
                    sh 'docker-compose down'
                }
            }
        }
        
        stage('Push Images to Docker Hub') {
            steps {
                script {
                    docker.withRegistry('https://registry.hub.docker.com', 'dockerhub_credentials') {
                        docker.image('api:latest').push('latest')
                        // Repeat for each image/tag combination if needed
                    }
                }
            }
        }
    }
}
