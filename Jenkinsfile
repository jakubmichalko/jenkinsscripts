pipeline {
    agent any
    
    stages {
        stage('List files') {
            steps {
                // files are alreadty cloned
                echo 'ls'
            }
        }
        stage('Prepare Image') {
            steps {
                sh 'docker build -t mywp .'
                withCredentials([string(credentialsId: 'dockerhub_token', variable: 'dhtoken')]) {
                    sh 'docker login -u jakubmichalko -p ${dhtoken}'
                
                }
                
                sh 'docker tag mywp jakubmichalko/mywp'
                sh 'docker push jakubmichalko/mywp'
            }
        }
        
        stage('run Kubernetes') {
            steps {
                sh 'kubectl create -f kub2.yml'
            }
        }
        
    }
}

