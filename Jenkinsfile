pipeline {
    agent any

    environment {
        // Customize these variables according to your project and environment
        JAVA_HOME = '/path/to/your/java/home'
        MAVEN_HOME = '/path/to/your/maven/home'
        DOCKER_REGISTRY = 'your-docker-registry.com'
        DOCKER_IMAGE_NAME = 'your-docker-image-name'
        DOCKER_IMAGE_TAG = 'latest'
    }

    stages {
        stage('Checkout') {
            steps {
                // Checkout the source code from version control
                checkout scm
            }
        }

        stage('Build') {
            steps {
                // Set up Java and Maven
                tool name: 'JDK', type: 'jdk', version: '11'
                tool name: 'Maven', type: 'maven', version: '3.8.1'

                // Build the Spring Boot application
                sh "${MAVEN_HOME}/bin/mvn clean install"
            }
        }

        stage('Test') {
            steps {
                // Run tests
                sh "${MAVEN_HOME}/bin/mvn test"
            }
        }

        stage('Build Docker Image') {
            steps {
                // Build Docker image
                script {
                    def dockerImage = docker.build("${DOCKER_REGISTRY}/${DOCKER_IMAGE_NAME}:${DOCKER_IMAGE_TAG}", "--build-arg JAR_FILE=target/*.jar .")
                }
            }
        }

        stage('Push to Docker Registry') {
            steps {
                // Log in to Docker registry
                script {
                    withCredentials([usernamePassword(credentialsId: 'docker-registry-credentials', usernameVariable: 'DOCKER_USERNAME', passwordVariable: 'DOCKER_PASSWORD')]) {
                        docker.withRegistry("${DOCKER_REGISTRY}", 'docker-registry-credentials') {
                            // Push Docker image to registry
                            dockerImage.push()
                        }
                    }
                }
            }
        }
    }
}
