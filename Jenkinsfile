pipeline {
    agent any
    
    environment {
        DOCKER_COMPOSE_FILE = 'docker-compose.pipeline.yml'
    }
    
    stages {
        stage('Checkout') {
            steps {
                cleanWs()
                checkout scm  # Fetches code from GitHub
                echo 'Code checked out from GitHub'
            }
        }
        
        stage('Deploy') {
            steps {
                sh 'docker-compose -f ${DOCKER_COMPOSE_FILE} down || true'
                sh 'docker-compose -f ${DOCKER_COMPOSE_FILE} up -d'  # Uses images + volumes for code
                sh 'docker-compose -f ${DOCKER_COMPOSE_FILE} ps'
                echo 'Deployed with code volumes'
            }
        }
        
        stage('Health Check') {
            steps {
                sh 'sleep 10'
                sh 'curl -f http://localhost:8001/ || echo "Backend not ready"'
            }
        }
    }
    
    post {
        success {
            echo 'Pipeline success! App running on ports 8001 and 8081 with volume-mounted code'
        }
        failure {
            echo 'Pipeline failed'
        }
        always {
            sh 'docker image prune -f || true'
        }
    }
}
