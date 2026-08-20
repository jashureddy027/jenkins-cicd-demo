pipeline {
    agent any

    environment {
        DOCKER_IMAGE = 'jashureddu027/jenkins-cicd-demo'
        DOCKER_CREDENTIALS = 'dockerhub-cradentials'
    }

    stages {

        stage('Checkout') {
            steps {
                echo 'Checking out code from GitHub...'

                git branch: 'main',
                    url: 'https://github.com/jashureddy027/jenkins-cicd-demo.git'
            }
        }

        stage('Docker Build') {
            steps {
                echo 'Building Docker image...'

                sh """
                    docker build -t ${DOCKER_IMAGE}:${BUILD_NUMBER} .
                    docker tag ${DOCKER_IMAGE}:${BUILD_NUMBER} ${DOCKER_IMAGE}:latest
                """
            }
        }

        stage('Docker Push') {
            steps {
                echo 'Pushing Docker image to Docker Hub...'

                withCredentials([
                    usernamePassword(
                        credentialsId: "${DOCKER_CREDENTIALS}",
                        usernameVariable: 'DOCKER_USERNAME',
                        passwordVariable: 'DOCKER_PASSWORD'
                    )
                ]) {

                    sh '''
                        echo "$DOCKER_PASSWORD" | docker login \
                            -u "$DOCKER_USERNAME" \
                            --password-stdin

                        docker push ${DOCKER_IMAGE}:${BUILD_NUMBER}

                        docker push ${DOCKER_IMAGE}:latest

                        docker logout
                    '''
                }
            }
        }
    }

    post {
        success {
            echo 'Docker image built and pushed successfully!'
        }

        failure {
            echo 'Pipeline failed!'
        }
    }
}
