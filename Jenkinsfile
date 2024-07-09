pipeline {
    agent {
        kubernetes {
            label 'docker'
            yaml """
apiVersion: v1
kind: Pod
metadata:
  labels:
    run: docker
  name: docker
spec:
  containers:
  - command:
    - sleep
    - "3600"
    image: docker
    name: docker
    volumeMounts:
    - mountPath: /var/run/docker.sock
      name: docker
  volumes:
  - name: docker
    hostPath:
      path: /var/run/docker.sock
            """
        }
    }
    parameters {
        string(name: 'IMAGE_TAG', defaultValue: 'latest', description: 'Docker image tag')
    }
    environment {
        DOCKER_CRED_ID = 'docker-creds'
    }
    stages {
        stage('Checkout SCM') {
            steps {
                git branch: 'main', url: 'https://github.com/Asyl0512/jenkinsss.git'
            }
        }
        stage('Docker Build') {
            steps {
                script {
                    dockerImage = docker.build("${env.DOCKER_USER}/apache:${params.IMAGE_TAG}")
                }
            }
        }
        stage('Docker Push') {
            steps {
                script {
                    docker.withRegistry('', DOCKER_CRED_ID) {
                        dockerImage.push()
                    }
                }
            }
        }
    }
    post {
        success {
            build job: 'CD-Pipeline', parameters: [
                string(name: 'IMAGE_TAG', value: "${params.IMAGE_TAG}")
            ]
        }
    }
}
