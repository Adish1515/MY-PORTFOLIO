pipeline {
    agent any

    environment {
        REPO_DIR = 'portfolio-anuj-1'
        REPO_URL = 'https://github.com/vanuj447/portfolio-anuj-1.git'
        EC2_USER = 'ubuntu'
        EC2_HOST = '3.84.118.150'
    }

    triggers {
        githubPush()
    }

    stages {
        stage('Deploy on EC2') {
            steps {
                echo '🚀 Starting deployment on EC2 instance...'

                sshagent(credentials: ['ec2-portfolio-key']) {
                    sh """
                        ssh -o StrictHostKeyChecking=no ${EC2_USER}@${EC2_HOST} '
                            set -e

                            echo "---- [1/9] Cleaning old Git data ----"
                            sudo rm -rf ~/.git ~/.config/git ~/.cache/git
                            sudo rm -rf ${REPO_DIR}

                            echo "---- [2/9] Installing dependencies ----"
                            sudo apt-get update -y >/dev/null 2>&1 || true
                            sudo apt-get install -y curl git >/dev/null 2>&1 || true

                            echo "---- [3/9] Installing Node.js & npm ----"
                            if ! command -v npm >/dev/null 2>&1; then
                                curl -fsSL https://deb.nodesource.com/setup_18.x | sudo -E bash - >/dev/null 2>&1
                                sudo apt-get install -y nodejs >/dev/null 2>&1
                            fi
                            node -v
                            npm -v

                            echo "---- [4/9] Cloning repository ----"
                            git clone ${REPO_URL}

                            echo "---- [5/9] Installing project dependencies ----"
                            cd ${REPO_DIR}
                            npm install --legacy-peer-deps

                            echo "---- [6/9] Building project ----"
                            npm run build

                            echo "---- [7/9] Deploying build to Nginx ----"
                            sudo rm -rf /var/www/html/*
                            sudo cp -r build/* /var/www/html/

                            echo "---- [8/9] Restarting Nginx ----"
                            sudo systemctl restart nginx

                            echo "✅ [9/9] Deployment completed successfully!"
                        '
                    """
                }
            }
        }
    }

    post {
        success {
            echo "🎉 Deployment successful! Application is live on http://${EC2_HOST}/"
        }
        failure {
            echo "❌ Deployment failed! Check EC2 logs for details."
        }
    }
}
