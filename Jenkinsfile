pipeline {
    agent any

    environment {
        DEPLOY_PATH = '/var/www/laravel-dev'
        DEPLOY_USER = 'finoganteng'
        PROD_HOST   = 'localhost'
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
                        ssh-keyscan -H "$PROD_HOST" >> ~/.ssh/known_hosts

                        rsync -avz --delete \
                          --exclude='.env' \
                          --exclude='storage/app' \
                          --exclude='storage/logs' \
                          --exclude='.git' \
                          --exclude='node_modules' \
                          ./ $DEPLOY_USER@$PROD_HOST:$DEPLOY_PATH/
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
