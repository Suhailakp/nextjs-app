pipeline {
    agent any

    tools {
        nodejs 'NodeJS' // Ensure this matches the NodeJS version in Jenkins Global Tools
    }

    environment {
        APP_NAME = 'nextjs-app'                     // PM2 process name
        DEPLOY_DIR = '/home/nextjs-app'          // Deployment target
    }

    stages {
        stage('Clone Repo') {
            steps {
                git branch: 'main', url: 'https://github.com/Suhailakp/nextjs-app.git'
            }
        }

        stage('Install Dependencies') {
            steps {
                sh 'npm install'
            }
        }

        stage('Build') {
            steps {
                sh 'npm run build'
            }
        }

        stage('Deploy') {
            steps {
                sh '''
                    echo "Deploying to $DEPLOY_DIR..."
                    rm -rf /home/nextjs-app/*
                    cp -r .next public node_modules package.json next.config.ts $DEPLOY_DIR/
                '''
            }
        }

        stage('Restart App with PM2') {
            steps {
                sh '''
                    cd $DEPLOY_DIR
                    pm2 start npm --name "$APP_NAME" -- start || pm2 restart "$APP_NAME"
                '''
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

