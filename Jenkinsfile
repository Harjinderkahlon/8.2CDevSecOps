pipeline {
  agent any

  stages {
    stage('Checkout') {
      steps {
        git branch: 'main', url: 'https://github.com/Harjinderkahlon/8.2CDevSecOps.git'
      }
    }

    stage('Detect npm') {
      steps {
        script {
          // try to find npm in container
          NPM_CMD = sh(script: 'which npm || echo npm', returnStdout: true).trim()
          echo "Using npm command: ${NPM_CMD}"
        }
      }
    }

    stage('Install dependencies') {
      steps {
        sh "${NPM_CMD} install"
      }
    }

    stage('Run tests') {
      steps {
        sh "${NPM_CMD} test 2>&1 | tee test-output.log || true"
        archiveArtifacts artifacts: 'test-output.log', fingerprint: true
      }
    }

    stage('Security audit') {
      steps {
        sh "${NPM_CMD} audit --json > npm-audit.json || true"
        sh "${NPM_CMD} audit 2>&1 | tee audit-output.log || true"
        archiveArtifacts artifacts: 'audit-output.log,npm-audit.json', fingerprint: true
      }
    }
  }

  post {
    always {
      echo "✅ Build finished. Logs saved as artifacts."
    }
  }
}
