pipeline {
    agent { label 'laravel' }
    stages {
        stage('Setup Tools') {
            steps {
                // Install curl safely for Telegram
                sh 'apt-get update || true'
                sh 'apt-get install -y curl || echo "Done"'
            }
        }
        stage('Build'){
            steps {
                sh 'cp .env.example .env || true'
                sh 'composer install --no-interaction --prefer-dist'
                sh 'npm install'
                sh 'php artisan key:generate'
            }
        }
        stage('Deploy'){
            steps {
                // Keep the server happy by deleting heavy folders before sending
                sh 'rm -rf vendor node_modules'
                sh 'ANSIBLE_HOST_KEY_CHECKING=False ansible-playbook -i inventory.ini deploy.yml'
            }
        }
    }
    post {
        success {
            sh 'curl -X POST https://api.telegram.org/bot8562338732:AAEonQH9tSU6pQ2cQuYp7lCC022NmHtvZK0/sendMessage -d chat_id=6084771232 -d text="✅ TP03 SUCCESS! http://178.128.93.188/socheatey"'
        }
        failure {
            sh 'curl -X POST https://api.telegram.org/bot8562338732:AAEonQH9tSU6pQ2cQuYp7lCC022NmHtvZK0/sendMessage -d chat_id=6084771232 -d text="❌ TP03 Build Failed!"'
        }
    }
}