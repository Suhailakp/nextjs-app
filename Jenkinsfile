pipeline {
  agent any

  tools {
    nodejs 'NodeJS' // Replace with the Node version name configured in Jenkins Global Tool Config
  }

  environment {
    DEPLOY_DIR = '/home/nextjs-app' // Your deploy target
  }

  stages {
    stage('Clone Repository') {
      steps {
        git branch: 'main', url: 'https://github.com/Suhailakp/nextjs-app.git'
      }
    }

    stage('Install Dependencies') {
      steps {
        sh 'npm ci' // use `npm install` if needed
      }
    }

    stage('Build Next.js App') {
      steps {
        sh 'npm run build'
      }
    }

    stage('Export Static Files') {
      steps {
        sh 'npm run export'
      }
    }

    stage('Deploy') {
      steps {
        // Example for local deployment
        sh 'rm -rf $DEPLOY_DIR/*'
        sh 'cp -r out/* $DEPLOY_DIR/'

        // OR for remote deployment
        // sh 'scp -r out/* user@yourserver:/path/to/deploy'
      }
    }
  }

  post {
    success {
      echo '✅ Deployment successful!'
    }
    failure {
      echo '❌ Deployment failed.'
    }
  }
}

