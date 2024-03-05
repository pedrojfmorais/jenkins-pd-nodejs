pipeline {
    agent any
    
    environment {
        branchName = Version.tokenize('/').last() //'refs/heads/main'
    }


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
                    withCredentials([usernamePassword(credentialsId: 'dockerhub', usernameVariable: 'docker_username', passwordVariable: 'docker_password')]) {
                        sh "docker login https://index.docker.io/v1/ --username \"\$docker_username\" --password \"\$docker_password\""
                    }                    
                }
            }
        }
        stage('Build and Publish FE') {
            when {
                expression {
                    return env.Component.contains('All') || env.Component.contains('Frontend') ;
                }
            }
            steps {
                script {
                    withCredentials([usernamePassword(credentialsId: 'dockerhub', usernameVariable: 'docker_username', passwordVariable: 'docker_password')]) {
                        sh '''
                        image_name="\"\$docker_username\"/docker-nodejs-demo:$branchName"
                        
                        docker build -t $image_name -f Dockerfile .
                        docker push $image_name
                        '''
                    } 
                }
            }
        }
        stage('Build and Publish BE') {
            when {
                expression {
                    return env.Component.contains('All') || env.Component.contains('Backend') ;
                }
            }
            steps {
                script {
                    withCredentials([usernamePassword(credentialsId: 'dockerhub', usernameVariable: 'docker_username', passwordVariable: 'docker_password')]) {
                        sh '''
                        image_name="\"\$docker_username\"/docker-nodejs-demo-backend:$branchName"
                        
                        docker build -t $image_name -f Dockerfile .
                        docker push $image_name
                        '''
                    } 
                }
            }
        }
    }
}

