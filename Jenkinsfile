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
        stage('Checkout') {
            steps {
                checkout scm
            }
        }
        
       
     stage('Build container') {
            steps {
                script {
                    docker.build("${IMAGE_NAME}")
                }
            }
        }
        stage('Run container') {
            steps {
                script {
                    docker.image(nginx/my-nginx)with.run('-d -p 9889:80')
                }
            }
        }
         stage('Verify MD5') {
            steps {
                script {
                    def filename = 'index.html'
                    def expectedMd5 = sh(script: "md5sum ${filename} | awk '{ print \$1 }'", returnStdout: true).trim()
                    def actualMd5 = sh(script: "curl -s http://localhost/${filename} | md5sum | awk '{ print \$1 }'", returnStdout: true).trim()
                    
                    if (expectedMd5 == actualMd5) {
                        echo "MD5 checksums match"
                    } else {
                        error "MD5 checksums do not match"
                    }
                }
            }
        }
    }
} 
