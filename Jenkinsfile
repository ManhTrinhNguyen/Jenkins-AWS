#!/usr/bin.env groovy
library identifier: 'jenkins-shared-library@main', retriever: modernSCM(
    [$class: 'GitSCMSource',
    remote: 'https://github.com/ManhTrinhNguyen/Jenkins-Docker-Excercise-Shared-Library.git',
    credentialsId: 'github-credentials'])
pipeline {   
    agent any
    tools {
        maven 'maven-3.9'
    }
    environment {
        IMAGE_NAME='nguyenmanhtrinh/demo-app:java-maven-1.0'
    }
    stages {
        stage("build jar") {
            steps {
                script {
                    echo "Building the application..."
                    buildMavenJar()
                }
            }
        }

        stage("build and push Docker image"){
            steps {
                script {
                    echo 'Build Docker Image'
                    buildDockerImage(env.IMAGE_NAME)
                    echo 'Login to docker hub'
                    dockerLoginToDockerHub()
                    echo 'Push to Docker hub'
                    pushDockerImage(env.IMAGE_NAME) 
                }
            }
        }

        stage("deploy") {
            steps {
                script {
                    echo "Deploying the application to EC2..."
                    def shellCMD = "bash ./server-cmds.sh"
                    sshagent(['ec2-server-key']) {
                        sh "scp docker-compose.yaml ec2-user@54.215.92.78:/home/ec2-user"
                        sh "scp server-cmds.sh ec2-user@54.215.92.78:/home/ec2-user"
                        sh "ssh -o StrictHostKeyChecking=no ec2-user@54.215.92.78 ${shellCMD}"
                    }
                }
            }
        }               
    }
} 
