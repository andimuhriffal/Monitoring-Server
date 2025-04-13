pipeline {
    agent { label 'agent-laravel' }

    environment {
        MONITORING_DIR = "${WORKSPACE}/monitoring"
    }

    stages {
        stage('Setup Monitoring Stack') {
            steps {
                echo "Navigating to monitoring directory: ${MONITORING_DIR}"
                dir("${MONITORING_DIR}") {
                    sh '''
                        echo "[INFO] Shutting down existing containers if any..."
                        docker-compose down || true

                        echo "[INFO] Starting monitoring stack (Prometheus, Grafana, Node Exporter)..."
                        docker-compose up -d
                    '''
                }
            }
        }

        stage('Check Services Status') {
            steps {
                sh '''
                    echo "[INFO] Checking running Docker containers..."
                    docker ps --format "table {{.Names}}\t{{.Status}}\t{{.Ports}}" | grep -E "prometheus|grafana|node-exporter" || echo "No monitoring containers found."
                '''
            }
        }
    }

    post {
        success {
            echo '✅ Monitoring stack deployed successfully!'
        }
        failure {
            echo '❌ Monitoring stack failed to deploy.'
        }
    }
}
