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
        
        stage('Deploy') {
            steps {
                sh 'docker rm -f taskflow-frontend-pipeline taskflow-backend-pipeline taskflow-db-pipeline 2>/dev/null || true'
                sh 'docker-compose -f ${DOCKER_COMPOSE_FILE} down || true'
                sh 'docker-compose -f ${DOCKER_COMPOSE_FILE} up -d'
                sh 'docker-compose -f ${DOCKER_COMPOSE_FILE} ps'
                echo 'Deployed with code volumes'
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
            echo 'Pipeline success! App running on ports 8081 and 8082'
        }
        failure {
            echo 'Pipeline failed'
        }
        always {
            sh 'docker image prune -f || true'
        }
    }
}
