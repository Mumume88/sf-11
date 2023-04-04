pipeline {
    agent any
    environment {
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
        
        stage('Build Docker Image') {
            steps {
                script {
                    docker.build("my-image:${env.BUILD_ID}")
                }
            }
        }
        
        stage('Run Docker Container') {
            steps {
                script {
                    def container = docker.run("-d -p 9889:80 my-image:${env.BUILD_ID}-v${pwd}/${FILE_PATH}:/usr/share/nginx/html/${FILE_PATH}")
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
post {
    always {
        docker.image("my-image:${env.BUILD_ID}").remove()
                 }
        }


