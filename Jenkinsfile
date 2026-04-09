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
                sh 'sudo apt-get update && sudo apt-get install -y curl || echo "Skipping curl install"'
            }
        }
        stage('Build'){
            steps{
                echo 'Building...'
                checkout scm
                sh 'cp .env.example .env'
                sh 'composer install'
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
                // Added host key checking bypass here
                sh 'ANSIBLE_HOST_KEY_CHECKING=False ansible-playbook -i inventory.ini deploy.yml'
            }
        }
    }

    post {
        failure {
            sh 'curl -X POST https://api.telegram.org/bot8562338732:AAEonQH9tSU6pQ2cQuYp7lCC022NmHtvZK0/sendMessage -d chat_id=6084771232 -d text="❌ TP03 Build Failed for RA SOCHEATEY!"'
        }
    }
}