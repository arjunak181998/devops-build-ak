pipeline {
    agent any

    environment {
        DEV_IMAGE  = "arjunak181998/dev:latest"
        PROD_IMAGE = "arjunak181998/prod:latest"
    }

    stages {

        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t app-image .'
            }
        }

        stage('Docker Login') {
            steps {
                withCredentials([
                    usernamePassword(
                        credentialsId: 'dockerhub',
                        usernameVariable: 'USER',
                        passwordVariable: 'PASS'
                    )
                ]) {
                    sh '''
                    echo $PASS | docker login -u $USER --password-stdin
                    '''
                }
            }
        }

        stage('Push Dev Image') {
            when {
                branch 'dev'
            }
            steps {
                sh '''
                docker tag app-image $DEV_IMAGE
                docker push $DEV_IMAGE
                '''
            }
        }

        stage('Push Prod Image') {
            when {
                branch 'master'
            }
            steps {
                sh '''
                docker tag app-image $PROD_IMAGE
                docker push $PROD_IMAGE
                '''
            }
        }

        stage('Deploy') {
            steps {
                sh '''
                docker compose down || true
                docker compose up -d
                '''
            }
        }
    }
}
