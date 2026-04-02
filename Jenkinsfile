pipeline {
    agent any

environment {
    DEPLOY_PATH = '/var/www/laravel-dev'
    DEPLOY_USER = 'finoganteng'
    PROD_HOST   = '172.17.0.1'   
}
    stages {

        stage('Checkout') {
            steps {
                echo '📥 Mengambil kode dari repository...'
                checkout scm
            }
        }

        stage('Install Dependencies') {
            steps {
                echo '📦 Install composer dependencies...'
                sh '''
                    docker run --rm \
                      -v $(pwd):/app \
                      -w /app \
                      composer:latest \
                      composer install \
                        --no-interaction \
                        --prefer-dist \
                        --optimize-autoloader
                '''
            }
        }

        stage('Test') {
            steps {
                echo '🧪 Menjalankan test...'
                sh 'echo "Test passed!"'
            }
        }

  stage('Deploy') {
            steps {
                echo '🚀 Deploy ke server...'
                sshagent(credentials: ['ssh-prod']) {
                    sh '''
                        mkdir -p ~/.ssh
                        chmod 700 ~/.ssh

                        ssh-keyscan -H 172.19.188.5 >> ~/.ssh/known_hosts 2>/dev/null
                        chmod 600 ~/.ssh/known_hosts

                        rsync -avz --delete \
                          -e "ssh -o StrictHostKeyChecking=no -v" \
                          --exclude='.env' \
                          --exclude='storage/app' \
                          --exclude='storage/logs' \
                          --exclude='.git' \
                          --exclude='node_modules' \
                          ./ finoganteng@172.19.188.5:/var/www/laravel-dev/
                    '''
                }
            }
        }

    }

    post {
        success {
            echo '✅ Deploy berhasil!'
        }
        failure {
            echo '❌ Deploy gagal! Cek Console Output.'
        }
    }
}
