node {
    checkout scm

    stage("Build") {
        docker.image('my-php-composer:8.4').inside('-u root') {
            sh 'php -v'
            sh 'git config --global --add safe.directory /var/jenkins_home/jobs/laravel-dev/workspace'
            sh 'composer install --no-interaction --prefer-dist --optimize-autoloader'
        }
    }

    stage("Testing") {
        docker.image('ubuntu').inside('-u root') {
            sh 'echo "Ini adalah test"'
        }
    }

    stage("Deploy Production") {
        docker.image('agung3wi/alpine-rsync:1.1').inside('-u root') {
            sshagent(credentials: ['ssh-prod']) {
                sh '''
                    mkdir -p ~/.ssh
                    ssh-keyscan -H "$PROD_HOST" >> ~/.ssh/known_hosts

                    rsync -rav --delete ./ \
                    finoganteng@$PROD_HOST:/home/finoganteng/prod.kelasdevops.xyz/ \
                    --exclude=.env \
                    --exclude=storage \
                    --exclude=.git
                '''
            }
        }
    }

    stage("Laravel Setup Production") {
        docker.image('agung3wi/alpine-rsync:1.1').inside('-u root') {
            sshagent(credentials: ['ssh-prod']) {
                sh '''
                    ssh finoganteng@$PROD_HOST "
                        cd /home/finoganteng/prod.kelasdevops.xyz &&
                        if [ ! -f .env ]; then cp .env.example .env; fi &&
                        php artisan key:generate --force
                    "
                '''
            }
        }
    }
}

