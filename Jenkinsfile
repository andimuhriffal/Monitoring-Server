pipeline {
    agent { label 'agent-laravel' }

    environment {
        MONITORING_DIR = "${WORKSPACE}/monitoring"
        MYSQL_ENV_TARGET = "${WORKSPACE}/monitoring/mysql.env"
    }

    stages {
        stage('Prepare MySQL Exporter Secret') {
            steps {
                withCredentials([file(credentialsId: 'MYSQL_ENV', variable: 'MYSQL_ENV')]) {
                    dir("${MONITORING_DIR}") {
                        sh '''
                            cp "$MYSQL_ENV" ./mysql.env
                        '''
                    }
                }
            }
        }

        stage('Setup Monitoring Stack') {
            steps {
                dir("${MONITORING_DIR}") {
                    sh '''
                        docker-compose down || true
                        docker-compose up -d --remove-orphans
                    '''
                }
            }
        }

        stage('Check Services Status') {
            steps {
                sh '''
                    docker ps --format "table {{.Names}}\t{{.Status}}\t{{.Ports}}" | grep -E "prometheus|grafana|node-exporter|mysql-exporter" || echo "No monitoring containers found."
                '''
            }
        }
    }

    post {
        always {
            dir("${MONITORING_DIR}") {
                sh 'rm -f mysql.env'
            }
        }

        success {
            echo '✅ Monitoring stack deployed successfully!'
        }
        failure {
            echo '❌ Monitoring stack failed to deploy.'
        }
    }
}
