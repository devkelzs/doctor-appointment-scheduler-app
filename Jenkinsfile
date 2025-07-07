pipeline {
    agent any

    environment {
        SONAR_HOST_URL = 'http://13.223.2.175:9000'
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'master', url: 'https://github.com/devkelzs/doctor-appointment-scheduler-app.git'
                echo 'Checking out..'
            }
        }

        stage('Build') {
            steps {
                sh 'chmod +x mvnw'
                sh './mvnw clean package -DskipTests=true'
                echo 'Building..'
            }
        }

        stage('Unit Tests') {
            steps {
                sh './mvnw test'
                echo 'Testing..'
            }
        }

        stage('SonarQube Analysis') {
            steps {
                withCredentials([string(credentialsId: 'sonar-token', variable: 'SONAR_TOKEN')]) {
                    sh """
                        ./mvnw sonar:sonar \
                        -Dsonar.host.url=$SONAR_HOST_URL \
                        -Dsonar.login=$SONAR_TOKEN
                    """
                    echo 'SonarQube analysis completed..'
                }
            }
        }
        stage('security analysis - OWASP Dependency Check') {
            steps {
                sh './mvnw org.owasp:dependency-check-maven:check -Dformat=ALL'
                echo 'OWASP Dependency Check completed..'
            }
        }
    }
}
