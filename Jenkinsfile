pipeline {
    agent any
    
    stages {
        stage('Preparation') {
            steps {
                checkout scm
                script {
                    commit_id = sh(returnStdout: true, script: "git rev-parse --short HEAD").trim()
                }
            }
        }
        
        stage('test') {
            agent {
                label 'nodejs'
            }
            steps {
                script {
                    nodejs(nodeJSInstallationName: 'nodejs') {
                        sh 'npm install --only=dev'
                        sh 'npm test'
                    }
                }
            }
        }
        
        stage('login to dockerhub') {
            steps {
                script {
                    docker.withRegistry('https://index.docker.io/v1/', 'dockerhub') {
                        def app = docker.login()
                    }
                }
            }
        }
    }
}

