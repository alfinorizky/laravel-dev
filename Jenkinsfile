pipeline {
    agent any

    environment {
        PROD_HOST = "172.19.188.5"
        PROD_USER = "finoganteng"
        PROD_DIR  = "/home/finoganteng/prod.kelasdevops.xyz"
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build') {
            steps {
                script {
                    docker.image('composer:2').inside('-u root') {
                        sh '''
                            php -v || true
                            composer --version
                            git config --global --add safe.directory "$WORKSPACE"
                            composer install --no-interaction --prefer-dist --optimize-autoloader || true
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
                script {
                    docker.image('agung3wi/alpine-rsync:1.1').inside('-u root') {
                        sshagent(credentials: ['ssh-prod']) {
                            sh '''
                                mkdir -p ~/.ssh
                                ssh-keyscan -H $PROD_HOST >> ~/.ssh/known_hosts

                                rsync -rav --delete ./ $PROD_USER@$PROD_HOST:$PROD_DIR/ \
                                  --exclude=.env \
                                  --exclude=storage \
                                  --exclude=.git \
                                  --exclude=vendor
                            '''
                        }
                    }
                }
            }
        }

        stage('Laravel Setup Production') {
            steps {
                script {
                    docker.image('agung3wi/alpine-rsync:1.1').inside('-u root') {
                        sshagent(credentials: ['ssh-prod']) {
                            sh '''
                                ssh $PROD_USER@$PROD_HOST "
                                    cd $PROD_DIR &&
                                    if [ ! -f .env ] && [ -f .env.example ]; then cp .env.example .env; fi
                                "
                            '''
                        }
                    }
                }
            }
        }
    }
}
