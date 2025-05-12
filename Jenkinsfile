pipeline {
    agent any
    
    environment {
        NODE_VERSION = '18'
        DOCKER_IMAGE = 'my-nextjs-app'
        DOCKER_TAG = "${env.BRANCH_NAME == 'main' ? 'latest' : env.BRANCH_NAME}"
    }
    
    tools {
        nodejs "Node ${NODE_VERSION}"
    }
    
    stages {
        stage('Prepare') {
            steps {
                script {
                    sh 'node --version'
                    sh 'npm --version'
                }
            }
        }
        
        stage('Install Dependencies') {
            steps {
                sh 'npm ci'
            }
        }
        
        stage('Lint') {
            steps {
                sh 'npm run lint'
            }
        }
        
        stage('Unit Test') {
            steps {
                sh 'npm run test'
            }
            post {
                always {
                    junit 'junit.xml'
                    cobertura coberturaReportFile: 'coverage/cobertura-coverage.xml'
                }
            }
        }
        
        stage('Build') {
            steps {
                sh 'npm run build'
            }
        }
        
        stage('Docker Build') {
            steps {
                script {
                    docker.build("${DOCKER_IMAGE}:${DOCKER_TAG}")
                }
            }
        }
        
        stage('Deploy') {
            when {
                branch 'main'
            }
            steps {
                script {
                    // Triển khai đến môi trường production
                    sh 'docker stop my-nextjs-container || true'
                    sh 'docker rm my-nextjs-container || true'
                    sh "docker run -d --name my-nextjs-container -p 3000:3000 ${DOCKER_IMAGE}:latest"
                }
            }
        }
    }
    
    post {
        failure {
            emailext (
                subject: "Jenkins Build Failed: ${currentBuild.fullDisplayName}",
                body: "Build failed in Jenkins: ${env.BUILD_URL}",
                to: 'your-email@example.com'
            )
        }
        
        success {
            echo 'Pipeline completed successfully!'
        }
        
        cleanup {
            cleanWs()
        }
    }
}