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
        
        stage('Clean') {
            steps {
                echo 'Nettoyage...'
                sh 'mvn clean'
            }
        }
        
        stage('Compile') {
            steps {
                echo ' Compilation...'
                sh 'mvn compile'
            }
        }
        
        stage('Test') {
            steps {
                echo ' Tests...'
                sh 'mvn test'
            }
        }
        
        stage('Package') {
            steps {
                echo ' Création du JAR...'
                sh 'mvn package'
            }
        }
    }
    
    post {
        success {
            echo 'Build réussi !'
            archiveArtifacts artifacts: '**/target/*.jar', allowEmptyArchive: true
        }
        failure {
            echo 'Build échoué !'
        }
    }
}
