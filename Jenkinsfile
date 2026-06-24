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

    stage('Debug Branch') {
        steps {
            sh '''
            echo "BRANCH_NAME=$BRANCH_NAME"
            echo "GIT_BRANCH=$GIT_BRANCH"
            '''
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

    stage('Deploy Dev') {
    when {
        expression {
            env.GIT_BRANCH?.contains('dev')
        }
    }
    steps {
        sh '''
        docker compose -f docker-compose.dev.yml down || true
        docker compose -f docker-compose.dev.yml up -d
        '''
    }
}

    stage('Deploy Prod') {
    when {
        expression {
            env.GIT_BRANCH?.contains('master')
        }
    }
    steps {
        sh '''
        docker compose -f docker-compose.prod.yml down || true
        docker compose -f docker-compose.prod.yml up -d
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

post {
    success {
        echo 'Pipeline completed successfully.'
    }
    failure {
        echo 'Pipeline failed.'
    }
}

}

