pipeline {
    agent { label 'agent-laravel' }

    environment {
        MONITORING_DIR = "${WORKSPACE}/monitoring"
    }

    stages {
        stage('Setup Monitoring Stack') {
            steps {
                dir("${MONITORING_DIR}") {
                    sh '''
                        docker-compose down || true
                        docker-compose up -d
                    '''
                }
            }
        }

        stage('Check Services Status') {
            steps {
                sh '''
                    docker ps | grep -E "prometheus|grafana|node-exporter"
                '''
            }
        }
    }

    post {
        success {
            echo 'Monitoring stack deployed successfully!'
        }
        failure {
            echo 'Monitoring stack failed to deploy.'
        }
    }
}
