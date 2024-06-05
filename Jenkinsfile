pipeline {
    agent any

    environment {
        SSH_CREDENTIALS_ID = "jenkins_agent"
        REMOTE_SERVER = "remote_server" // Adres IP lub nazwa hosta zdalnego serwera
        DOCKERFILE_PATH = "docker-compose.yml" // Zmienna środowiskowa do określenia ścieżki dockerfilee
    }

    stages {
        stage('Clone Repository') {
            steps {
                checkout([$class: 'GitSCM', 
                          branches: [[name: '*/main']],
                          userRemoteConfigs: [[url: 'https://github.com/DevOpsAcademyTeamC/-app-repo-.git']]])
            }
        }

        stage('Build and Push Test Images') {
            steps {
                sh 'docker-compose build -f ${DOCKERFILE_PATH}'
                sh 'docker-compose push'
            }
        }

        stage('Run Tests') {
            steps {
                sshagent (credentials: [SSH_CREDENTIALS_ID]) {
                    sh "ssh jenkins@${REMOTE_SERVER} 'docker-compose up -d'"
                    sh "ssh jenkins@${REMOTE_SERVER} 'docker exec saleor-platform_api_1 bash -c \"cd ./saleor/graphql/checkout/tests && pytest\"'"
                    sh "ssh jenkins@${REMOTE_SERVER} 'docker-compose down'"
                }
            }
        }

        stage('Promote to Production') {
            steps {
                sshagent (credentials: [SSH_CREDENTIALS_ID]) {
                    sh "ssh jenkins@${REMOTE_SERVER} 'docker-compose pull'"
                    sh "ssh jenkins@${REMOTE_SERVER} 'docker-compose up -d'"
                }
            }
        }
    }

    post {
        always {
            sshagent (credentials: [SSH_CREDENTIALS_ID]) {
                sh "ssh jenkins@${REMOTE_SERVER} 'docker-compose down'"
            }
        }
    }
}
