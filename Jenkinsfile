pipeline {
    agent any

    environment {
        PROD_DIR = "$WORKSPACE/deploy"
    }

    stages {
        stage('Build') {
            steps {
                script {
                    docker.image('composer:2').inside('--entrypoint="" -u root') {
                        sh '''
                            php -v || true
                            composer --version
                            git config --global --add safe.directory "$WORKSPACE"
                            composer install --no-interaction --prefer-dist --optimize-autoloader
                        '''
                    }
                }
            }
        }

        stage('Testing') {
            steps {
                sh 'echo "Ini adalah test"'
            }
        }

        stage('Deploy Production') {
            steps {
                sh '''
                    mkdir -p "$PROD_DIR"
                    rsync -rav --delete ./ "$PROD_DIR"/ \
                      --exclude=.env \
                      --exclude=storage \
                      --exclude=.git \
                      --exclude=vendor
                '''
            }
        }

        stage('Laravel Setup Production') {
            steps {
                sh '''
                    cd "$PROD_DIR"
                    if [ ! -f .env ] && [ -f .env.example ]; then
                      cp .env.example .env
                    fi
                    ls -la
                '''
            }
        }
    }
}
