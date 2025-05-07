pipeline {
    agent any

    environment {
        IMAGE_NAME = 'flask-boilerplate'
        DOCKER_TAG = 'latest'
        DOCKER_REPO = "docker.io/$DOCKER_USERNAME/$IMAGE_NAME"
    }

    stages {
        stage('Clone Repo') {
            steps {
                git 'https://github.com/tarannumshaikh-projects/flask-boilerplate-devops.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    sh 'docker build -t $DOCKER_REPO:$DOCKER_TAG .'
                }
            }
        }

        stage('Push Docker Image to DockerHub') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'dockerhub-creds',
                    usernameVariable: 'DOCKER_USERNAME',
                    passwordVariable: 'DOCKER_PASSWORD'
                )]) {
                    sh '''
                        echo "$DOCKER_PASSWORD" | docker login -u "$DOCKER_USERNAME" --password-stdin
                        docker push $DOCKER_REPO:$DOCKER_TAG
                    '''
                }
            }
        }
    }
}

