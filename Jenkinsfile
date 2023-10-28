#!/usr/bin/env groovy

library identifier: 'jenkins-shared-library@main', retriever: modernSCM(
        [$class: 'GitSCMSource',
         remote: 'git@github.com:sidor2/devops-module8-jenkins-shared-lib.git',
         credentialsId: '2c40c606-3564-4fc4-8fc2-3a89a016f089',
        ]
)

pipeline {
    agent any

    tools {
        nodejs 'nodejs-20'
    }

    environment {
        DOCKER_IMAGE = 'module8app'
        DOCKER_REGISTRY = 'ilsoldier/devops'
        GIT_CREDENTIALS = '2c40c606-3564-4fc4-8fc2-3a89a016f089'
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Run Tests') {
            steps {
                dir('app'){
                    sh 'npm install'
                    sh 'npm test'
                }
            }
        }

        stage('Increment Version') {
            steps {
                script {
                    dir('app'){
                        sh 'npm version patch'

                        def packageJson = readJSON file: 'package.json'
                        def version = packageJson.version

                        // set the new version as part of IMAGE_NAME
                        env.IMAGE_NAME = "$version-$BUILD_NUMBER"

                    }
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    buildImage "ilsoldier/devops:node-app-$env.IMAGE_NAME"
                    dockerLogin()
                    dockerPush "ilsoldier/devops:node-app-$env.IMAGE_NAME"
                }
            }
        }

        stage('commit to git'){
            steps{
                script{
                    commitToGithub("2c40c606-3564-4fc4-8fc2-3a89a016f089","devops-module8-exercises","feature/solutions")
                }
            }
        }
    }

    post {
        failure {
            echo 'Pipeline failed!'
        }
    }
}
