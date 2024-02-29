pipeline {
    agent {
        label 'FOCUS-builds'
    }
    stages {
      stage('Checkout repo on selected version') {
            steps {
                sh 'rm -rf repo_dir'
                withCredentials([usernamePassword(credentialsId: 'acm', usernameVariable: 'svn_username', passwordVariable: 'svn_password')]) {
                    sh 'svn checkout https://svn.ptin.corppt.com/repo/acm/$Version --username $svn_username --password "$svn_password" repo_dir'
                }
            }
      }
    

    }

}


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

