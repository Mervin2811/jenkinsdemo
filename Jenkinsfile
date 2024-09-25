pipeline {
    agent any 
    environment {
        DOCKERHUB_CREDENTIALS = credentials('test1')
    }
    stages {
        stage('Build docker image') {
            steps {  
                echo "Building Docker image..."
                sh 'docker build -t mervin2811/flaskapp:$BUILD_NUMBER .'
            }
        }
        stage('login to dockerhub') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'test1', usernameVariable: 'DOCKERHUB_USERNAME', passwordVariable: 'DOCKERHUB_PASSWORD')]) {
                    echo "Logging in to Docker Hub..."
                    // Using --password-stdin for better security
                    sh 'echo $DOCKERHUB_PASSWORD | docker login -u $DOCKERHUB_USERNAME --password-stdin'
                }
            }
        }
        stage('push image') {
            steps {
                echo "Pushing Docker image to Docker Hub..."
                // Correct Docker image push
                sh 'docker push mervin2811/flaskapp:$BUILD_NUMBER'
            }
        }
        stage('Deploy to Staging') {
            steps {
                // Pull the latest image from Docker Hub
                sh 'docker pull mervin2811/flaskapp:$BUILD_NUMBER'

                // Stop and remove any existing containers
                sh 'docker stop myapp-container || true'
                sh 'docker rm myapp-container || true'

                // Run the new container with the latest image
                sh 'docker run -d --name myapp-container -p 8000:8000 mervin2811/flaskapp:$BUILD_NUMBER'
            }
        }
    }
    post {
        always {
            echo "Logging out from Docker Hub..."
            sh 'docker logout'
        }
    }
}
