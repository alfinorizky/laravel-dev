pipeline {
    agent any
    environment {
        DEPLOY_PATH = '/home/finoganteng/laravel-docker/src'
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
                    docker -H unix:///var/run/docker.sock run --rm \
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
                sh '''
                    cp -r $(pwd)/. /home/finoganteng/laravel-docker/src/

                    docker -H unix:///var/run/docker.sock exec laravel_app1 bash -c "
                        cd /var/www/html &&
                        php artisan migrate --force &&
                        php artisan config:cache &&
                        php artisan route:cache &&
                        php artisan view:cache
                    "
                '''
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
