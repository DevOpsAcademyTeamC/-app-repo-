pipeline {
    agent any

    environment {
        SSH_CREDENTIALS_ID = "jenkins_agent"
        SSH_SERVER = "jenkins@localhost"
    }

    stages {
        stage('Clone repo') {
            steps {
                git 'https://github.com/DevOpsAcademyTeamC/-app-repo-'
            }
        }

        stage('Build and Push Test Images') {
            steps {
                sshagent (credentials: [SSH_CREDENTIALS_ID]) {
                    sh 'docker-compose build'
                    sh 'docker-compose push'
                }
            }
        }

        stage('Run Tests') {
            steps {
                sshagent (credentials: [SSH_CREDENTIALS_ID]) {
                    sh 'docker-compose up -d'  
                    sh 'docker exec api bash -c "cd ./saleor/graphql/checkout/tests && pytest"'  
                    sh 'docker-compose down'  
                }
            }
        }

        stage('Promote to Production') {
            steps {
                sshagent (credentials: [SSH_CREDENTIALS_ID]) {
                    sh 'docker-compose pull'  
                    sh 'docker-compose up -d'  
                }
            }
        }
    }

    post {
        always {
            sshagent (credentials: [SSH_CREDENTIALS_ID]) {
                sh 'docker-compose down'  
            }
        }
    }
}
