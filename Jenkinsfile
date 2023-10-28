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

                    echo "Current version: ${packageJson.version}"

                    def (major, minor, patch) = packageJson.version.split('\\.').collect { it.toInteger() }

                    patch++

                    def nextVersion = "${major}.${minor}.${patch}"

                    echo "Next version: ${nextVersion}"

                    packageJson.version = nextVersion
                    writeJSON file: filePath, json: packageJson

                    echo "Version updated in package.json"
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
