pipeline {
    agent any

    environment {
            DOCKER_CREDENTIALS = credentials("docker-secret")
            IMAGE_NAME = 'pranavagrawalcs/survey-app'
            IMAGE_NAME_TEST = "pranavagrawalcs/survey-app:${BUILD_TIMESTAMP}"
            BUILD_TIMESTAMP_DEPLOYMENT = "${BUILD_TIMESTAMP}"
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }
        stage('Build and Deploy') {
            steps {
                script {
                    sh 'echo ${IMAGE_NAME_TEST}'
                    sh 'echo ${BUILD_TIMESTAMP_DEPLOYMENT}'

                    sh 'docker login -u pranavagrawalcs -p ${DOCKER_CREDENTIALS}'
                    sh 'docker build -t pranavagrawalcs/survey-app:${BUILD_TIMESTAMP} .'
                    sh 'docker push pranavagrawalcs/survey-app:${BUILD_TIMESTAMP}'
                    sh 'docker image rm pranavagrawalcs/survey-app:${BUILD_TIMESTAMP} -f'

                    sh 'kubectl apply -f service.yaml'
                    sh 'envsubst < deployment.yaml > deployment_resolved.yaml'
                    sh 'cat deployment_resolved.yaml'
                    sh 'kubectl apply -f deployment_resolved.yaml'
                }
            }
        }
    }

    post {
        success {
            echo 'Build and installation succeeded!'
        }
        failure {
            echo 'Build or installation failed!'
        }
    }
}
