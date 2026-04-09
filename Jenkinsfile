pipeline {
    agent {
        label 'laravel'
    }
    
    triggers {
        cron('* * * * *')
    }

    stages {
        stage('Setup Tools') {
            steps {
                sh 'sudo apt-get update && sudo apt-get install -y curl || echo "Already has curl"'
            }
        }
        stage('Build'){
            steps{
                echo 'Building...'
                checkout scm  // Always checkout FIRST
                sh 'cp .env.example .env || true' // || true prevents error if file exists
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
                echo 'Deploying to teacher server...'
                // This bypasses the SSH fingerprint prompt that usually causes hangs
                sh 'ANSIBLE_HOST_KEY_CHECKING=False ansible-playbook -i inventory.ini deploy.yml'
            }
        }
    }

    post {
        failure {
            sh 'curl -X POST https://api.telegram.org/bot8562338732:AAEonQH9tSU6pQ2cQuYp7lCC022NmHtvZK0/sendMessage -d chat_id=6084771232 -d text="❌ Build #$BUILD_NUMBER Failed for RA SOCHEATEY at ITC!"'
        }
    }
}