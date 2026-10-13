pipeline {
    agent any

    environment {
        IMAGE_NAME = 'aceest-fitness-devops'
        CONTAINER_NAME = 'aceest-fitness-jenkins-smoke'
    }

    stages {
        stage('Install and Test') {
            steps {
                sh 'python -m pip install --user -r requirements.txt'
                sh 'python -m pytest -q tests'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh "docker build --tag ${IMAGE_NAME}:${BUILD_NUMBER} ."
            }
        }

        stage('Container Smoke Test') {
            steps {
                sh "docker run --detach --rm --name ${CONTAINER_NAME} --publish 5099:5000 ${IMAGE_NAME}:${BUILD_NUMBER}"
                sh 'python -c "import time, urllib.request; time.sleep(5); assert urllib.request.urlopen(\"http://127.0.0.1:5099/healthz\").status == 200"'
            }
        }
    }

    post {
        always {
            sh "docker stop ${CONTAINER_NAME} || true"
            sh "docker image rm ${IMAGE_NAME}:${BUILD_NUMBER} || true"
        }
    }
}
