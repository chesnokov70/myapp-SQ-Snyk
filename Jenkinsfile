pipeline {
  agent any

  environment {
    SONAR_TOKEN = credentials('jenkins_token')
    SNYK_TOKEN = credentials('snyk-token')
  }

  stages {
    stage('Checkout') {
      steps { checkout scm }
    }

    stage('Install Dependencies') {
      steps {
        sh 'bash -c "python3 -m venv venv && source venv/bin/activate && pip install -r requirements.txt"'

      }
    }

    stage('SonarQube Scan') {
      steps {
        withSonarQubeEnv('SonarCloud') {
          sh 'sonar-scanner -Dsonar.projectKey=myapp -Dsonar.sources=.'
        }
      }
    }

    stage('Snyk Scan') {
      steps {
        sh '''
          snyk auth ${SNYK_TOKEN}
          snyk test || true
        '''
      }
    }

    stage('Trivy FS Scan') {
      steps {
        sh 'trivy fs . || true'
      }
    }

    stage('Build Docker Image') {
      steps {
        sh 'docker build -t myapp:latest .'
      }
    }

    stage('Trivy Image Scan') {
      steps {
        sh 'trivy image myapp:latest || true'
      }
    }
  }
}

