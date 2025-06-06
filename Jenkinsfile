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
                echo "🔍 Checking container status..."
                sh '''
                    echo "[INFO] Running containers:"
                    docker ps --format "table {{.Names}}\t{{.Status}}\t{{.Ports}}" \
                        | grep -Ei "prometheus|grafana|node-exporter|mysqld-exporter" \
                        || echo "[WARN] No expected monitoring containers are currently running."
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
