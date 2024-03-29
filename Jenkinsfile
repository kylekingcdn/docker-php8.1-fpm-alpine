pipeline {
    agent any
    environment {
        image_name = 'kylekingcdn/php8.1-fpm-alpine'
        image_tag = 'latest'
        registry_endpoint = ''
        registry_credentials = 'dockerhub-credentials'
    }
    stages {
        stage('Build Images') {
            steps {
                script {
                    docker.withRegistry('', registry_credentials) {
                        image = docker.build(image_name+':'+image_tag, "\
                            --force-rm \
                            --no-cache \
                            --pull \
                            .")
                    }
                }
            }
        }
        stage('Push Images') {
            when {
                environment name: 'GIT_BRANCH', value: 'main'
            }
            steps {
                script {
                    docker.withRegistry('', registry_credentials) {
                        image.push()
                    }
                }
            }
        }
    }
    post {
        cleanup {
            sh label: "Remove image ", script: """
                docker image rm ${image_name}:${image_tag} || true;
            """
            sh label: "Prune images ", script: """
                docker image prune --force;
            """
            cleanWs()
        }
    }
}
