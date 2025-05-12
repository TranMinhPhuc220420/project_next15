pipeline {
  agent any

  stages {
    stage('Install dependencies') {
      steps {
        sh 'npm ci' // Hoặc 'npm install' nếu không dùng lockfile
      }
    }

    stage('Run Tests') {
      steps {
        sh 'npm run test:ci'
      }
    }

    stage('Archive coverage') {
      steps {
        junit 'coverage/junit.xml' // nếu bạn chuyển coverage sang định dạng junit
        publishHTML(target: [
          reportName : 'Coverage Report',
          reportDir  : 'coverage',
          reportFiles: 'index.html',
        ])
      }
    }
  }
}
