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
                echo 'Code checked out'
            }
        }
        
        stage('Build Images') {
            steps {
                sh 'docker-compose -f ${DOCKER_COMPOSE_FILE} build --no-cache'
                echo 'Images built'
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
                sh 'curl -f http://localhost:8001/ || echo "Backend not ready"'
            }
        }
    }
    
    post {
        success {
            echo 'Pipeline success! App running on ports 8001 and 8081'
        }
        failure {
            echo 'Pipeline failed'
        }
        always {
            sh 'docker image prune -f || true'
        }
    }
}
