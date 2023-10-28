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
                sh 'npm install'
                sh 'npm test'
            }
        }

        stage('Increment Version') {
            steps {
                script {
                    def filePath = 'app/package.json'
                    def packageJson = readJSON file: filePath
                    echo "Current version is ${packageJson.version}"
                    def versionParts = packageJson.version.split('\\.')
                    echo "Split version is ${versionParts}"
                    def patchVersion = versionParts[2].toInteger()
                    patchVersion++
                    packageJson.version = "${versionParts[0]}.${versionParts[1]}.${patchVersion}"
                    echo "New version is ${packageJson.version}"
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
