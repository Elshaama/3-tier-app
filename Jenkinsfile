pipeline {
    agent any

    environment {
        DOCKER_REGISTRY = 'Elshaama' // Replace with your Docker registry URL
        DOCKER_CREDENTIALS_ID = 'docker-credentials' // Replace with your Docker credentials ID in Jenkins
        REPO_URL = 'https://github.com/Elshaama/3-tier-app.git' // Replace with your Git repository URL
        BRANCH = 'main' // Replace with your branch name
    }

    stages {
        stage('Checkout Code') {
            steps {
                git branch: "${env.BRANCH}", url: "${env.REPO_URL}"
            }
        }

        stage('Build Docker Images') {
            parallel {
                stage('Build Backend Image') {
                    steps {
                        script {
                            sh 'docker build -t backend-image:latest -f /backend-flask .'
                        }
                    }
                }
                stage('Build Frontend Image') {
                    steps {
                        script {
                            sh 'docker build -t frontend-image:latest -f /frontend-html .'
                        }
                    }
                }
                
            }
        }

        stage('Push Docker Images') {
            parallel {
                stage('Push Backend Image') {
                    steps {
                        script {
                            withDockerRegistry([credentialsId: "${env.DOCKER_CREDENTIALS_ID}", url: "${env.DOCKER_REGISTRY}"]) {
                                sh 'docker tag backend-image:latest ${env.DOCKER_REGISTRY}/backend-image:latest'
                                sh 'docker push ${env.DOCKER_REGISTRY}/backend-image:latest'
                            }
                        }
                    }
                }
                stage('Push Frontend Image') {
                    steps {
                        script {
                            withDockerRegistry([credentialsId: "${env.DOCKER_CREDENTIALS_ID}", url: "${env.DOCKER_REGISTRY}"]) {
                                sh 'docker tag frontend-image:latest ${env.DOCKER_REGISTRY}/frontend-image:latest'
                                sh 'docker push ${env.DOCKER_REGISTRY}/frontend-image:latest'
                            }
                        }
                    }
                }
                stage('Push Database Image') {
                    steps {
                        script {
                            withDockerRegistry([credentialsId: "${env.DOCKER_CREDENTIALS_ID}", url: "${env.DOCKER_REGISTRY}"]) {
                                sh 'docker tag database-image:latest ${env.DOCKER_REGISTRY}/database-image:latest'
                                sh 'docker push ${env.DOCKER_REGISTRY}/database-image:latest'
                            }
                        }
                    }
                }
            }
        }

        stage('Deploy with Docker Compose') {
            steps {
                script {
                    sh 'docker-compose -f docker-compose.yml up -d'
                }
            }
        }
    }

    post {
        always {
            cleanWs()
        }
    }
}
