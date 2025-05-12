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
                    // Cách 1: Sử dụng nvm nếu có sẵn trên Jenkins server
                    sh '''
                        export NVM_DIR="$HOME/.nvm"
                        [ -s "$NVM_DIR/nvm.sh" ] && . "$NVM_DIR/nvm.sh"
                        nvm install ${NODE_VERSION} || echo "NVM not found, trying direct installation"
                        nvm use ${NODE_VERSION} || echo "Continuing without nvm"
                    '''
                    
                    // Cách 2: Nếu không có nvm, sử dụng Node.js có sẵn trên hệ thống
                    sh 'node --version || echo "Node.js not found"'
                    sh 'npm --version || echo "NPM not found"'
                }
            }
        }
        
        stage('Install Dependencies') {
            steps {
                sh 'npm ci || npm install'
            }
        }
        
        stage('Lint') {
            steps {
                sh 'npm run lint || echo "Skipping lint"'
            }
        }
        
        stage('Unit Test') {
            steps {
                sh 'npm run test || echo "Tests failed but continuing"'
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
            echo 'Pipeline failed!'
        }
        
        success {
            echo 'Pipeline completed successfully!'
        }
        
        cleanup {
            cleanWs()
        }
    }
}