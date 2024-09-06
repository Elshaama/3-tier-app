pipeline {
    agent any

    environment {
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
                stage('Build Database Image') {
                    steps {
                        script {
                            dir('database-mysql') { // Adjust the directory if needed
                                sh 'docker-compose build -t database-image:latest .'
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
                                sh 'docker tag backend-image:latest docker.io/elshaama/backend-image:latest'
                                sh 'docker push docker.io/elshaama/backend-image:latest'
                            }
                        }
                    }
                }
                stage('Push Frontend Image') {
                    steps {
                        script {
                            withDockerRegistry([credentialsId: "${env.DOCKER_CREDENTIALS_ID}"]) {
                                sh 'docker tag frontend-image:latest docker.io/elshaama/frontend-image:latest'
                                sh 'docker push docker.io/elshaama/frontend-image:latest'
                            }
                        }
                    }
                }
                stage('Push Database Image') {
                    steps {
                        script {
                            withDockerRegistry([credentialsId: "${env.DOCKER_CREDENTIALS_ID}"]) {
                                sh 'docker tag database-image:latest docker.io/elshaama/database-image:latest'
                                sh 'docker push docker.io/elshaama/database-image:latest'
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
