pipeline {
    agent any
    
    tools {
        maven 'M2_HOME'
        jdk 'JAVA_HOME'
    }
    
        stage('Checkout') {
            steps {
                echo 'Récupération du code...'
                checkout scm
            }
        }
        
}
