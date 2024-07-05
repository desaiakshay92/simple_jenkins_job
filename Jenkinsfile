pipeline {
    agent any
    environment {
        DOCKER_IMAGE = 'desaiakshay92/jenkin_app:latest'
    }

    stages {
        stage('Installation Check on server') {
            steps {
                script {
                    // Check if Git is installed
                    if (sh(script: 'git --version', returnStatus: true) != 0) {
                        error "Git is not installed or not found in the PATH"
                    }

                    // Check if Docker is installed
                    if (sh(script: 'docker --version', returnStatus: true) != 0) {
                        error "Docker is not installed or not found in the PATH"
                    }
                }
            }
        }
        stage('SCM Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/desaiakshay92/simple_jenkins_job.git'
            }
        }
        stage('Pre-Install Check for Application') {
            steps {
                script {
                    // Check if Dockerfile is available
                    if (!fileExists('Dockerfile')) {
                        error "DockerFile is not available in repo"
                    }
                }
            }
        }
        stage('Validate Docker Service is running') {
            steps {
                script {
                    // Check Docker is active
                    if (sh(script: 'sudo systemctl is-active docker', returnStatus: true) != 0) {
                        error "Docker is not Active"
                    }
                }
            }
        }
        stage('Build Docker Image') {
            steps {
                script {
                    // Build Docker image
                    def dockerImage = docker.build("${DOCKER_IMAGE}", '.')
                }
            }
        }
        stage('Unit Test') {
            steps {
                // Example: Run tests using Docker container
                sh "docker run --rm ${DOCKER_IMAGE} python test_main.py"
                // Replace 'python test.py' with your actual test command
            }
        }
        stage('Push Docker Image to Docker Hub') {
            steps {
                // Log in to Docker Hub
                withCredentials([usernamePassword(credentialsId: 'Docker_Creds', usernameVariable: 'DOCKER_HUB_USERNAME', passwordVariable: 'DOCKER_HUB_PASSWORD')]) {
                    sh "docker login -u ${DOCKER_HUB_USERNAME} -p ${DOCKER_HUB_PASSWORD}"
                }
                // Push Docker image to Docker Hub
                sh "docker push ${DOCKER_IMAGE}"
            }
        }
    }
}
