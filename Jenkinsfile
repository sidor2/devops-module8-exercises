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

        stage('Increment Version') {
            steps {
                script {
                    def packageJson = readJSON file: 'app/package.json'
                    def versionParts = packageJson.version.split('\\.')
                    def patchVersion = versionParts[2].toInteger()
                    patchVersion++
                    packageJson.version = "${versionParts[0]}.${versionParts[1]}.${patchVersion}"
                    writeJSON file: 'package.json', json: packageJson
                    env.NEW_VERSION = packageJson.version
                    sh "echo ${env.NEW_VERSION}"
                }
            }
        }

        stage('Run Tests') {
            steps {
                sh 'npm install'
                sh 'npm test'
            }
        }
    }

    post {
        failure {
            echo 'Pipeline failed!'
        }
    }
}
