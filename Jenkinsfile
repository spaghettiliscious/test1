pipeline {
    agent any

    environment {
        DOCKER_HUB_USER = 'spaghettiliscious'   // cambia con il tuo username Docker Hub
        IMAGE_NAME = 'nginx'         // repository name su Docker Hub
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Docker Build') {
            steps {
                script {
                    // Costruisce immagine docker e la salva in variabile app
                    app = docker.build("${DOCKER_HUB_USER}/${IMAGE_NAME}")
                }
            }
        }

        stage('Test Image') {
            steps {
                script {
                    sh """
                        docker run -d --name app-test -p 8090:80 ${DOCKER_HUB_USER}/${IMAGE_NAME}
                        sleep 5
                        curl -f http://localhost:8080/index.html || (echo "Test fallito" && exit 1)
                        docker stop app-test
                        docker rm app-test
                    """
                }
            }
        }

        stage('Push to Docker Hub') {
            steps {
                script {
                    docker.withRegistry('https://registry.hub.docker.com', 'docker-hub-credentials') {
                        // Push con tag build number
                        app.push("${env.BUILD_NUMBER}")
                        // Push anche come latest
                        app.push("latest")
                    }
                }
            }
        }
    }
}

