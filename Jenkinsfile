pipeline {
    agent any

    environment {
        DOCKER_IMAGE = 'ndata-test-ms'
        DOCKER_REGISTRY = 'docker.io'
        DOCKER_REGISTRY_CREDENTIALS = 'docker-hub-credentials'
        KUBE_NAMESPACE = 'your-kubernetes-namespace'
        KUBE_DEPLOYMENT = 'your-kubernetes-deployment'
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build') {
            steps {
                sh 'mvn clean install'
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    def customImageTag = "${DOCKER_REGISTRY}/${DOCKER_IMAGE}:${BUILD_NUMBER}"
                    def dockerImage = docker.build(customImageTag, '.')
                    withCredentials([usernamePassword(credentialsId: DOCKER_REGISTRY_CREDENTIALS, usernameVariable: 'lcrbneves', passwordVariable: '2Sq9he3c!')]) {
                        docker.withRegistry(DOCKER_REGISTRY, DOCKER_USER, DOCKER_PASSWORD) {
                            dockerImage.push()
                        }
                    }
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                script {
                    sh "kubectl config use-context k8app"
                    sh "kubectl set image deployment/${KUBE_DEPLOYMENT} your-container-name=${DOCKER_IMAGE}:${BUILD_NUMBER} -n ${KUBE_NAMESPACE}"
                }
            }
        }

        stage('Clean Up') {
            steps {
                sh 'mvn clean'
            }
        }
    }

    post {
        success {
            echo 'Deployment successful'
        }
        failure {
            echo 'Deployment failed'
        }
    }
}