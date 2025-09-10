pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/karris12/demo.git'
            }
        }

        stage('Build') {
            steps {
                sh './mvnw clean package -DskipTests'
            }
        }

        stage('Deploy') {
            steps {
                // Stop old app (if running)
                sh 'pkill -f demo-0.0.1-SNAPSHOT.jar || true'

                // Run new app in background
                sh 'nohup java -jar target/demo-0.0.1-SNAPSHOT.jar > app.log 2>&1 &'
            }
        }
    }
}
