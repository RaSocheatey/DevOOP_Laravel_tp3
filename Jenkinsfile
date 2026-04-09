pipeline {
    agent {
        label 'laravel'
    }
    
    triggers {
        cron('* * * * *')
    }

    stages {
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
                echo 'Verifying Inventory File...'
                // This checks if the file exists and shows its content in the log
                sh 'ls -l inventory/hosts.ini && cat inventory/hosts.ini'
                
                echo 'Deploying to teacher server...'
                // Use the -i flag and ensure the path is correct
                sh 'ansible-playbook -i inventory/hosts.ini deploy.yml'
            }
        }
    }

    post {
        failure {
            sh 'curl -X POST https://api.telegram.org/bot8562338732:AAEonQH9tSU6pQ2cQuYp7lCC022NmHtvZK0/sendMessage -d chat_id=6084771232 -d text="❌ TP03 Build Failed for RA SOCHEATEY!"'
        }
    }
}