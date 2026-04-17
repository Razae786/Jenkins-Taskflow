pipeline {
    agent any
    
    environment {
        DOCKER_COMPOSE_FILE = 'docker-compose.pipeline.yml'
    }
    
    stages {
        stage('Checkout') {
            steps {
                cleanWs()
                checkout scm
                echo 'Code checked out from GitHub'
            }
        }
        
        stage('Build Frontend') {
            steps {
                sh 'cd frontend && npm install && npm run build'
                echo 'Frontend built'
            }
        }
        
        stage('Deploy') {
            steps {
                sh 'docker-compose -f ${DOCKER_COMPOSE_FILE} down || true'
                sh 'docker-compose -f ${DOCKER_COMPOSE_FILE} up -d'
                sh 'docker-compose -f ${DOCKER_COMPOSE_FILE} ps'
                echo 'Deployed'
            }
        }
        
        stage('Health Check') {
            steps {
                sh 'sleep 10'
                sh 'curl -f http://localhost:8081/'
            }
        }
    }
    
    post {
        success {
            echo 'Pipeline success!'
        }
        failure {
            echo 'Pipeline failed'
        }
        always {
            sh 'docker image prune -f || true'
        }
    }
}
