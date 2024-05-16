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
        
        stage('Build Docker Image') {
            steps {
                script {
                    docker.withServer('unix:///home/jakub-marzewski/.docker/desktop/docker-cli.sock') {
                        docker.build('saleor-platform:latest', '.')
                    }
                }
            }
        }
        
        stage('Run Unit Tests') {
            steps {
                script {
                    docker.image('saleor-platform:latest').inside {
                        sh 'pytest'
                    }
                }
            }
        }
        
        stage('Push Image to Docker Hub') {
            steps {
                script {
                    docker.withRegistry('https://registry.hub.docker.com', 'dockerhub_credentials') {
                        docker.image('saleor-platform:latest').push('latest')
                    }
                }
            }
        }
    }
}
