node {
    checkout scm

    stage("Build") {
        docker.image('my-php-composer:8.4').inside('-u root') {
            sh 'php -v'
            sh 'git config --global --add safe.directory /var/jenkins_home/jobs/laravel-dev/workspace'
            sh 'composer install'
        }
    }

    stage("Testing") {
        docker.image('ubuntu').inside('-u root') {
            sh 'echo "Ini adalah test"'
        }
    }
}

