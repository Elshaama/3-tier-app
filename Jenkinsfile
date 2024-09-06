pipeline {
    agent any

    environment {
        DOCKER_CREDENTIALS_ID = 'docker-credential' // Replace with your Docker credentials ID in Jenkins
        REPO_URL = 'https://github.com/Elshaama/3-tier-app.git' // Replace with your Git repository URL
        BRANCH = 'main' // Replace with your branch name
        DOCKER_HUB_REPO = 'elshaama'
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
                            dir('backend-flask') {
                                sh 'docker build -t backend-image:latest .'
                            }
                        }
                    }
                }
                stage('Build Frontend Image') {
                    steps {
                        script {
                            dir('frontend-html') {
                                sh 'docker build -t frontend-image:latest .'
                            }
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
                                withDockerRegistry([credentialsId: "${env.DOCKER_CREDENTIALS_ID}"]) {
                                sh 'docker tag backend-image:latest ${env.DOCKER_HUB_REPO}/backend-image:latest'
                                sh 'docker push ${env.DOCKER_HUB_REPO}/backend-image:latest'
                            }
                        }
                    }
                }
                stage('Push Frontend Image') {
                    steps {
                        script {
                            withDockerRegistry([credentialsId: "${env.DOCKER_CREDENTIALS_ID}", url: "${env.DOCKER_REGISTRY_URL}"]) {
                                sh 'docker tag frontend-image:latest ${env.DOCKER_REGISTRY_URL}/elshaama/frontend-image:latest'
                                sh 'docker push ${env.DOCKER_REGISTRY_URL}/elshaama/frontend-image:latest'
                            }
                        }
                    }
                }
                stage('Push Database Image') {
                    steps {
                        script {
                            withDockerRegistry([credentialsId: "${env.DOCKER_CREDENTIALS_ID}", url: "${env.DOCKER_REGISTRY_URL}"]) {
                                sh 'docker tag database-image:latest ${env.DOCKER_REGISTRY_URL}/elshaama/database-image:latest'
                                sh 'docker push ${env.DOCKER_REGISTRY_URL}/elshaama/database-image:latest'
                            }
                        }
                    }
                }
            }
        }

        stage('Deploy with Docker Compose') {
            steps {
                script {
                    dir('database-mysql') { // Ensure this directory is correct
                        sh 'docker-compose -f docker-compose.yml up -d'
                    }
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
