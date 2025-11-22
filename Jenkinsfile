pipeline {

    agent any

    tools {
        maven 'maven'        // Do NOT change - must match Maven tool name in Jenkins
    }

    environment {
        APP_SERVER_IP = "172.31.25.217"     // 🔵 CHANGE HERE: Put your App Server PRIVATE IP
        APP_USER      = "ubuntu"            // 🔵 CHANGE if your EC2 username is different
        APP_NAME      = "springbootapp"     // Optional: name for logs & process
    }

    stages {

        stage('Checkout Code') {
            steps {
                echo "===== Pulling Code from GitHub ====="
                git branch: 'main', url: 'https://github.com/sinchanac2617/springboot_without_docker.git'
                // 🔵 CHANGE URL if your GitHub repo name is different
            }
        }

        stage('Build JAR File') {
            steps {
                sh """
                    mvn clean package -DskipTests
                """
            }
        }

        stage('Copy Artifact to App Server') {
            steps {
                sh """
                    scp -o StrictHostKeyChecking=no target/*.jar ${APP_USER}@${APP_SERVER_IP}:/home/${APP_USER}/app.jar
                    // 🔵 Ensure SSH key is configured in Jenkins Credentials
                """
            }
        }

        stage('Deploy Spring Boot Application') {
            steps {
                sh """
                    ssh -o StrictHostKeyChecking=no ${APP_USER}@${APP_SERVER_IP} "
                        pkill -f app.jar || true
                        nohup java -jar /home/${APP_USER}/app.jar --server.port=8082 > app.log 2>&1 &
                    "
                """
            }
        }
    }

    post {
        success {
            echo "🎉 SUCCESS: App deployed to App Server and running on port 8082"
        }
        failure {
            echo "❌ BUILD/DEPLOY FAILED"
        }
    }
}
