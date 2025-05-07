pipeline {
    agent any

    environment {
        IMAGE_NAME = 'flast-boilerplate'   // Must match DockerHub repo
        DOCKER_TAG = 'latest'
    }

    stages {
        stage('Clone Repo') {
            steps {
                git 'https://github.com/tarannumshaikh-projects/flask-boilerplate-devops.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'dockerhub-creds',
                    usernameVariable: 'DOCKER_USERNAME',
                    passwordVariable: 'DOCKER_PASSWORD'
                )]) {
                    script {
                        def dockerRepo = "docker.io/${DOCKER_USERNAME}/${IMAGE_NAME}"
                        sh "docker build -t ${dockerRepo}:${DOCKER_TAG} ."

                        sh """
                            echo "${DOCKER_PASSWORD}" | docker login -u "${DOCKER_USERNAME}" --password-stdin
                            docker push ${dockerRepo}:${DOCKER_TAG}
                        """
                    }
                }
            }
        }
    }
}

