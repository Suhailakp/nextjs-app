pipeline {
    agent any

    tools {
        nodejs 'NodeJS' // Ensure this matches the NodeJS version in Jenkins Global Tools
    }

    environment {
        APP_NAME = 'nextjs-app'                     // PM2 process name
        DEPLOY_DIR = '/home/nextjs-app'          // Deployment target
        PORT = '3000'                            // Application port
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
                    # Create deployment directory if it doesn't exist
                    mkdir -p $DEPLOY_DIR
                    
                    # Stop existing PM2 process if running
                    pm2 stop $APP_NAME || true
                    pm2 delete $APP_NAME || true
                    #rm -rf /home/nextjs-app/*
   
                    # Copy application files
                    cp -r .next public node_modules package.json next.config.ts $DEPLOY_DIR/
                    
                     # Copy next.config.ts if it exists, otherwise skip
                    [ -f next.config.ts ] && cp next.config.ts $DEPLOY_DIR/ || echo "next.config.ts not found, skipping..."
                  
                    # Set proper permissions
                    chmod -R 755 $DEPLOY_DIR
                '''
            }
        }

        stage('Restart App with PM2') {
            steps {
                sh '''
                    cd $DEPLOY_DIR
                    # Method 1: Start with npm start
                    pm2 start npm --name "$APP_NAME" -- start
                    
                    # Alternative Method 2: Direct next start (uncomment if Method 1 fails)
                    # pm2 start "npx next start -p $PORT" --name "$APP_NAME"
                    
                    # Wait a moment for the app to start
                    sleep 5
                    
                    # Save PM2 configuration
                    pm2 save
                    
                    # Show PM2 status
                    pm2 list
                 '''
            }
        }
        tage('Health Check') {
            steps {
                sh '''
                    echo "Waiting for application to start..."
                    sleep 10
                    
                    # Check if the app is responding
                    curl -f http://localhost:$PORT || (echo "Health check failed" && exit 1)
                    
                    echo "✅ Application is running and responding!"
                '''
            }
        }
    }

    post {
        success {
            echo '✅ Deployment successful!'
            sh 'pm2 list'
        }
        failure {
            echo '❌ Deployment failed.'
            sh '''
                echo "=== PM2 Status ==="
                pm2 list || true
                echo "=== PM2 Logs ==="
                pm2 logs $APP_NAME --lines 50 || true
                echo "=== Port Check ==="
                sudo netstat -tlnp | grep :$PORT || true
            '''
        }
    


