
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

        stage('Check Node, npm and Docker') {
            steps {
                sh '''
                    node --version
                    npm --version
                    docker --version
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

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t rajan9650/qb-ecommerce:v3 .'
            }
        }

        stage('Push Docker Image') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'dockerhub-credentials',
                    usernameVariable: 'DOCKERHUB_USERNAME',
                    passwordVariable: 'DOCKERHUB_TOKEN'
                )]) {
                    sh '''
                        set +x
                        echo "$DOCKERHUB_TOKEN" | docker login \
                            --username "$DOCKERHUB_USERNAME" \
                            --password-stdin
                        docker push rajan9650/qb-ecommerce:v3
                        docker logout
                    '''
                }
            }
        }

        stage('Deploy to EKS') {
            steps {
                sh '''
                    set -e

                    kubectl set image deployment/qb-ecommerce \
                      qb-ecommerce=rajan9650/qb-ecommerce:v3 \
                      -n qbshop

                    kubectl rollout status deployment/qb-ecommerce \
                      -n qbshop --timeout=180s

                    kubectl get deployment qb-ecommerce -n qbshop
                '''
            }
        }


    }



    post {
        success {
            echo 'CI/CD image build and Docker Hub push completed successfully.'
        }
        failure {
            echo 'Pipeline failed. Check the console output.'
        }
    }
}

