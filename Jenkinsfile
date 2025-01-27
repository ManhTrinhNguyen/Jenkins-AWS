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
    
    stages {
        stage("Increment Version") {
            steps {
                script {
                    sh 'mvn build-helper:parse-version version:set -DnewVersion=\${parsedVersion.majorVersion}.\${parsedVersion.minorVersion}.\${parsedVersion.nextIncrementalVersion} versions:commit'
                       def matcher = readFile('pom.xml') =~ '<version>(.+)</version>'
                       def version = matcher[0][1]
                       env.IMAGE_NAME="$version-$BUILD_NUMBER"
                }
            }
        }

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

                    def shellCMD = "bash ./server-cmds.sh ${IMAGE_NAME}"
                    def ec2Instance = 'ec2-user@54.215.92.78'
                    sshagent(['ec2-server-key']) {
                        sh "scp docker-compose.yaml ${ec2Instance}:/home/ec2-user"
                        sh "scp server-cmds.sh ${ec2Instance}:/home/ec2-user"
                        sh "ssh -o StrictHostKeyChecking=no ${ec2Instance} ${shellCMD}"
                    }
                }
            }
        } 

        stage("Automatic Commit the new version to Git Repo") {
            steps {
                script{
                    withCredentials([usernamePassword(credentialsId: 'github-credentials', passwordVariable: 'PASS', usernameVariable: 'USER')]){
                        sh "git remote set-url orgin https://${USER}:${PASS}@github.com/ManhTrinhNguyen/Jenkins-AWS.git"
                        
                        sh 'git config --global user.email aws@example.com'
                        sh 'git config --global user.name AWS'

                        sh 'git status'
                        sh 'git config --list'
                        sh 'git branch'

                        sh 'git add .'
                        sh 'git commit -m "ci:bump version"'
                        sh 'git push origin HEAD:main'
                    }
                    
                }
            }
        }              
    }
} 
