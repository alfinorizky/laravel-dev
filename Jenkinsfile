node {
    checkout scm

    stage("Build") {
        docker.image('my-php-composer:8.2').inside('-u root') {
            sh 'php -v'
            sh 'composer install'
        }
    }

    stage("Testing") {
        docker.image('ubuntu').inside('-u root') {
            sh 'echo "Ini adalah test"'
        }
    }
}

