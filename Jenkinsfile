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
        stage('Deploy') {
            steps {
                echo '🚀 Deploy ke server...'
                sh '''
                    # Copy file ke folder mount laravel
                    cp -r $(pwd)/. /home/finoganteng/laravel-docker/src/

                    # Install dependencies dan jalankan artisan di container
                    docker -H unix:///var/run/docker.sock exec laravel_app1 bash -c "
                        cd /var/www/html &&
                        composer install --no-interaction --optimize-autoloader &&
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
