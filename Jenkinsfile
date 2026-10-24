
pipeline {
    agent any

    options {
        timestamps()
        disableConcurrentBuilds()
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Check Node and npm') {
            steps {
                sh '''
                    node --version
                    npm --version
                '''
            }
        }

        stage('Install Dependencies') {
            steps {
                sh 'npm ci'
            }
        }

        stage('Build Application') {
            steps {
                sh 'npm run build'
            }
        }
    }

    post {
        success {
            echo 'QB-Ecommerce CI pipeline completed successfully.'
        }
        failure {
            echo 'QB-Ecommerce CI pipeline failed. Check the console output.'
        }
    }
}

