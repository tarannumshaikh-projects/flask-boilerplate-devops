pipeline {
    agent any

    environment {
        IMAGE_NAME = 'flast-boilerplate'       // Change this if your image name is different
        DOCKER_TAG = 'latest'
	KUBECONFIG = "${HOME}/.kube/config" // Set path to kubeconfig
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

        stage('Deploy to Kubernetes') {
            steps {
                script {
                    def dockerRepo = "docker.io/${env.DOCKER_USERNAME}/${env.IMAGE_NAME}"
                    sh """
                        # Replace image name in deployment YAML
                        sed -i 's|IMAGE_PLACEHOLDER|${dockerRepo}:${DOCKER_TAG}|' k8s/deployment.yaml

                        # Apply Kubernetes manifests
                        kubectl apply -f k8s/deployment.yaml
                        kubectl apply -f k8s/service.yaml
                    """
                }
            }
        }
    }
}

   
