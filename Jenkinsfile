pipeline {
    agent any

    environment {
        APP_PORT = '8081'
        APP_LOG_FILE = "${env.WORKSPACE}/app.log"
        APP_PID_FILE = "${env.WORKSPACE}/app.pid"
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build') {
            steps {
                // Make mvnw executable
                sh 'chmod +x mvnw'

                // Clean and compile project (skip tests)
                sh './mvnw clean compile -DskipTests'
            }
        }

        stage('Deploy') {
            steps {
                // Stop any previous instance
                sh "pkill -f 'spring-boot:run' || true"

                // Start app in background using spring-boot:run
                sh """
                nohup ./mvnw spring-boot:run \
                -Dspring-boot.run.arguments="--server.port=${APP_PORT},--server.address=0.0.0.0" \
                > ${APP_LOG_FILE} 2>&1 & disown
                echo \$! > ${APP_PID_FILE}
                """
            }
        }

        stage('Verify') {
            steps {
                // Wait a few seconds and verify app is running
                sh """
                sleep 5
                if ps -p \$(cat ${APP_PID_FILE}) > /dev/null; then
                  echo '✅ App is running'
                else
                  echo '❌ App failed to start'
                  cat ${APP_LOG_FILE}
                  exit 1
                fi
                """
            }
        }
    }

    post {
        always {
            echo "Logs are available at: ${env.WORKSPACE}/app.log"
        }
    }
}
