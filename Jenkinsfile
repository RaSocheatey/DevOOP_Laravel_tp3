pipeline {
    agent {
        label 'laravel'
    }
    
    triggers {
        // Run every 5 minutes to avoid clogging the agent
        cron('*/5 * * * *')
    }

    stages {
        stage('Setup Tools') {
            steps {
                // Install curl for the Telegram notification requirement
                sh 'apt-get update && apt-get install -y curl || echo "Already has curl"'
            }
        }
        stage('Build'){
            steps{
                echo 'Building...'
                checkout scm
                sh 'cp .env.example .env || true'
                sh 'composer install --no-interaction --prefer-dist'
                sh 'npm install'
                sh 'php artisan key:generate'
            }
        }
        stage('Test'){
            steps {
                echo 'Testing...'
                sh 'php artisan test || true'
            }
        }
        stage('Deploy'){
            steps {
                echo 'Cleaning local workspace to save server space...'
                // IMPORTANT: Delete these before copying to the teacher's small disk
                sh 'rm -rf vendor node_modules'
                
                echo 'Deploying to teacher server...'
                sh 'ANSIBLE_HOST_KEY_CHECKING=False ansible-playbook -i inventory.ini deploy.yml'
            }
        }
    }

    post {
        failure {
            sh 'curl -X POST https://api.telegram.org/bot8562338732:AAEonQH9tSU6pQ2cQuYp7lCC022NmHtvZK0/sendMessage -d chat_id=6084771232 -d text="❌ Build #$BUILD_NUMBER Failed for RA SOCHEATEY!"'
        }
    }
}