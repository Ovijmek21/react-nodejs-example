#!/usr/bin/env groovy

library identifier: 'jenkins-shared-library@master', retriever: modernSCM(
    [$class: 'GitSCMSource',
    remote: 'https://github.com/Ovijmek21/jenkins-shared-repository.git',
    credentialsId: 'github-cred'
    ]
)

pipeline {
    agent any
    tools {
    nodejs 'nodejs-24.5.0'
    }
    environment {
        IMAGE_NAME = 'ovijmek21/react-nodejs-example:1.1'
    }

    stages {
        stage("Checkout") {
            steps {
                script {
                    checkOut()
                }
            }
        }

        stage('build app') {
            steps {
                    echo "Building the application..."
                    buildJar()
            }
        }
        stage('build image') {
            steps {
                script {
                    echo "Build docker image"
                    buildImg(env.IMAGE_NAME)
                    dockerLogin()
                    pushImg(env.IMAGE_NAME)
                }
            }
        }
        stage('deploy') {
            steps {
                script {
                    echo 'deploy the new image'
                    def dockerCmd = "docker run -p 3080:3080 -d '${IMAGE_NAME}'"
                    sshagent(['ec2-server-key']) {
                        sh "ssh -o StrictHostKeyChecking=no ec2-user@35.158.192.106 '${dockerCmd}'"
                    }
                }
            }
        }
    }
}
