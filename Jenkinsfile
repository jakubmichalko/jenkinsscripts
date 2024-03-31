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
            }
        }
        
        stage('run Kubernetes') {
            steps {
                sh 'kubectl create -f kubwp.yml'
            }
        }
        
    }
}

