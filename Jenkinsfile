pipeline {
    agent { 
        docker { 
            image 'node:latest' 
        }
    }
    stages {
        stage('Install Dependencies') {
            steps {
                sh 'npm install'
            }
        }
        stage('Run Tests') {
            steps {
                sh 'npm test'
            }
        }
        stage('Report Test Results') {
            steps {
                // Assuming Jest produces a JUnit-style report
                junit 'reports/jest.xml'
            }
        }
    }
    post {
        always {
            cleanWs()
        }
    }
}