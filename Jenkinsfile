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
                // Ensure mvnw is executable
                sh 'chmod +x mvnw'
                sh './mvnw clean package -DskipTests'
            }
        }

        stage('Deploy') {
            steps {
                // Stop any old app (optional safety)
                sh "pkill -f 'demo-0.0.1-SNAPSHOT.jar' || true"

                // Run new app as Jenkins user with logs in /tmp
                sh """
                nohup java -jar target/demo-0.0.1-SNAPSHOT.jar \
                --server.port=8081 \
                > /tmp/app.log 2>&1 &
                """
            }
        }
    }

    post {
        success {
            echo '✅ Deployment successful! App should be running on port 8081'
        }
        failure {
            echo '❌ Build/Deploy failed. Check logs.'
        }
    }
}
