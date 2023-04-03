pipeline {
    agent any
    environment {
        IMAGE_NAME = 'nginx'
        CONTAINER_NAME = 'my-nginx'
        REPO_URL = 'https://github.com/Mumume88/sf-11.git'
        FILE_PATH = 'index.html'
    }
    stages {
        stage('Clone repository') {
            steps {
                git branch: 'main', url: REPO_URL
            }
        }
        stage('Download file') {
            when {
                changeset "*/${FILE_PATH}"
            }
            steps {
                sh "wget ${REPO_URL}/raw/main/${FILE_PATH}"
            }
        }
        stage('Build and run container') {
            steps {
                script {
                    docker.build("${IMAGE_NAME}")
                    docker.run("-p 9889:80 --name ${CONTAINER_NAME} -v ${pwd}/${FILE_PATH}:/usr/share/nginx/html/${FILE_PATH}:ro ${IMAGE_NAME}")
                }
            }
        }
    }
} 
