pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build') {
            steps {
                // Make sure mvnw is executable
                sh 'chmod +x mvnw'
                sh './mvnw clean package -DskipTests'
            }
        }

       stage('Deploy') {
    steps {
        sh "pkill -f 'demo-0.0.1-SNAPSHOT.jar' || true"
        sh """
        nohup java -jar target/demo-0.0.1-SNAPSHOT.jar \
--server.port=8081 --server.address=0.0.0.0 \
> $WORKSPACE/app.log 2>&1 & disown
echo $! > $WORKSPACE/app.pid

        """
    }
}
        stage('Verify') {
            steps {
                // Wait a bit and check if app is running
                sh """
                sleep 5
                if ps -p \$(cat app.pid) > /dev/null; then
                  echo '✅ App started successfully'
                else
                  echo '❌ App failed to start'
                  exit 1
                fi
                """
            }
        }
    }

    post {
        always {
            script {
                echo "Logs available at workspace: ${env.WORKSPACE}/app.log"
            }
        }
    }
}
