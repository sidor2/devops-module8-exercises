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
                    println "Current version is ${packageJson}"
                    echo "Current version is ${packageJson.version}"
                    def versionParts = packageJson.version.split('\\.')
                    echo "Split version is ${versionParts}"
                    def patchVersion = versionParts[2].toInteger()
                    patchVersion++
                    echo "Major version " + versionParts[0]
                    echo "Minor version " + versionParts[1]
                    echo "New patch version " + patchVersion
                    nextVersion = "${versionParts[0]}.${versionParts[1]}.${patchVersion}"
                    echo "Next version is ${nextVersion}"
                    packageJson.version = nextVersion.toString()
                    writeJSON file: filePath, json: packageJson
                    println "New version is ${packageJson}"
                }
            }
        }

        stage('commit and push') {
            steps {
                script {
                    sshagent(['2c40c606-3564-4fc4-8fc2-3a89a016f089']) {
                        sh 'git config --global user.email "jenkins@example.com"'
                        sh 'git config --global user.name "jenkins"'

                        sh 'git status'
                        sh 'git branch'
                        sh 'git config --list'

                        sh "git remote set-url origin git@github.com:sidor2/devops-module8-exercises.git"
                        sh 'git add .'
                        sh 'git commit -m "ci: version bump"'
                        sh 'git push origin HEAD:feature/solutions'
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
