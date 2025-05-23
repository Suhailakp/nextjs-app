pipeline {
    agent any

    tools {
        nodejs 'NodeJS' // Replace with the NodeJS name you configured
    }

    environment {
        DEPLOY_DIR = '/home/nextjs-app'
        BUILD_DIR = '.next'
    }

    stages {
        stage('Clone Repository') {
            steps {
                git branch: 'main', url: 'https://github.com/Suhailakp/nextjs-app.git'
            }
        }

        stage('Install Dependencies') {
            steps {
                sh 'npm install'
            }
        }

        stage('Build Next.js App') {
            steps {
                sh 'npm run build'
            }
        }

        stage('Export Static Files (Optional)') {
            steps {
                sh 'npm run export'
                // Only needed if you are using `next export`
            }
        }

        stage('Deploy') {
            steps {
                sh '''
                    echo "Deploying build to $DEPLOY_DIR..."
                    rm -rf $DEPLOY_DIR/*
                    cp -r .next static public package.json $DEPLOY_DIR/
                    echo "Deployment complete!"
                '''
            }
        }

        stage('Start App (optional)') {
            steps {
                sh '''
                    cd $DEPLOY_DIR
                    npm install --omit=dev
                    pm2 restart next-app || pm2 start npm --name "next-app" -- start
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

