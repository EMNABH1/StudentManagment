pipeline {
    agent any
    
    tools {
        maven 'M2_HOME'
        jdk 'JAVA_HOME'
    }
    
    stages {
        stage('Checkout') {
            steps {
                echo 'Récupération du code...'
                checkout scm
            }
        }
stage('Compile') {
            steps {
                echo ' Compilation...'
                sh 'mvn compile'
            }
        }
        

        
        
    }
}
