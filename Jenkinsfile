pipeline {
    agent any

    environment {
        SONAR_HOST_URL = 'http://13.223.2.175:9000'
        NVD_API_KEY = credentials('NVD_API_KEY') // Securely load OWASP API key
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'master', url: 'https://github.com/devkelzs/doctor-appointment-scheduler-app.git'
                echo 'Checking out repository...'
            }
        }

        stage('Build') {
            steps {
                sh 'chmod +x mvnw'
                sh './mvnw clean package -DskipTests=true'
                echo 'Build complete.'
            }
        }

        stage('Unit Tests') {
            steps {
                sh './mvnw test'
                echo 'Unit tests completed.'
            }
        }

        stage('SonarQube Analysis') {
            steps {
                withCredentials([string(credentialsId: 'sonar-token', variable: 'SONAR_TOKEN')]) {
                    sh """
                        ./mvnw sonar:sonar \
                        -Dsonar.host.url=$SONAR_HOST_URL \
                        -Dsonar.token=$SONAR_TOKEN
                    """
                    echo 'SonarQube analysis completed.'
                }
            }
        }

        stage('Security Analysis - OWASP Dependency Check') {
            steps {
                // Optional: Safe preview of first 4 characters of API key
                sh 'echo "Using NVD API Key (prefix): $(echo $NVD_API_KEY | cut -c1-4)****"'

                // Run OWASP Dependency Check with API key
                sh './mvnw org.owasp:depen
