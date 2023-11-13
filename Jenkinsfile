pipeline {
    agent any
    tools {
        jdk 'Java17'
        maven "Maven3"
    }

    environment {
        APP_NAME = "complete-prodcution-e2e-pipeline"
        RELEASE = '1.0.0'
        DOCKER_USER = "Deleany"
        DOCKER_PASS = "dokcehub-token"
        IMAGE_NAME = "${DOKCE_USER}/${APP_NAME}"
        IMAGE_TAG = "${RELEASE}-${BUILD_NUMBER}"
    }
    stages {
        stage('Cleanup Workspace') {
            steps {
                cleanWs()
            }
        }
        stage('Checkout from SCM') {
            steps {
                git branch: 'main', credentialsId: 'github', url: 'git@github.com:Deleany/complete-prodcution-e2e-pipeline.git'
            }
        }
        stage('Build App') {
            steps {
                sh "mvn clean package"
            }
        }
        
        stage('Test App') {
            steps {
                sh "mvn test"
            }
        }
        
        stage('Sonarqube Analysis') {
            steps {
                script {
                withSonarQubeEnv(credentialsId: 'sonarqube-token') {
                    sh "mvn sonar:sonar"
                }               
                }
            }
        }
        
        stage('Quality Gate') {
            steps {
                script {
                    waitForQualityGate abortPipeline: false, credentialsId: 'sonarqube-token'
                }
                
            }
        }

        stage('Build & Push Docker Image') {
            steps {
                script {
                    withDockerRegistry(credentialsId: DOCKER_PASS) {
                        docker_image = docker.Build "${IMAGE_NAME}"

                    }
                }
                
            }
        }
        
    }
}
