pipeline {
    agent any
    
    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }
        
        stage('Compile') {
            steps {
                dir('student-management') {
                    sh '/opt/maven/bin/mvn clean compile'
                }
            }
        }
        
        stage('Tests') {
            steps {
                dir('student-management') {
                    sh '/opt/maven/bin/mvn test'
                }
            }
        }
        
        stage('Package') {
            steps {
                dir('student-management') {
                    sh '/opt/maven/bin/mvn package -DskipTests'
                }
            }
        }
    }
    
    post {
        success {
            echo '✅ Build réussi !'
            archiveArtifacts artifacts: 'student-management/target/*.jar'
        }
    }
}
