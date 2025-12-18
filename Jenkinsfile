pipeline {
    agent any
    
    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }
        
        stage('Debug') {
            steps {
                sh 'pwd'
                sh 'ls -la'
                sh 'find . -name "pom.xml"'
            }
        }
    }
}
