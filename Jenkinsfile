pipeline {
    agent any
    
    environment {
        NODE_VERSION = '18'
        DOCKER_IMAGE = 'my-nextjs-app'
        DOCKER_TAG = "${env.BRANCH_NAME == 'main' ? 'latest' : env.BRANCH_NAME}"
    }
    
    stages {
        stage('Setup Node.js') {
            steps {
                script {
                    def nodeHome = tool name: 'NodeJS', type: 'jenkins.plugins.nodejs.tools.NodeJSInstallation'
                    env.PATH = "${nodeHome}/bin:${env.PATH}"
                }
                sh 'node --version'
                sh 'npm --version'
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
                    echo 'Collecting test reports...'
                    // Nếu bạn đã cấu hình xuất báo cáo JUnit, sử dụng dòng dưới đây
                    // junit 'junit.xml'
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
                    sh "docker build -t ${DOCKER_IMAGE}:${DOCKER_TAG} ."
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
            echo 'Pipeline failed! Sending notification...'
            // Thay thế bằng cách thông báo phù hợp với hệ thống của bạn
            // mail to: 'your-email@example.com', subject: 'Pipeline failed', body: 'Check Jenkins for details'
        }
        
        success {
            echo 'Pipeline completed successfully!'
        }
        
        cleanup {
            cleanWs()
        }
    }
}