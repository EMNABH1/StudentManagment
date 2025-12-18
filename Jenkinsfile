pipeline {
    agent any
    
    stages {
        stage('📥 Checkout') {
            steps {
                echo '📥 Récupération du code...'
                checkout scm
            }
        }
        
        stage('🔨 Compile') {
            steps {
                echo '🔨 Compilation...'
                dir('StudentManagment/student-management') {
                    sh '/opt/maven/bin/mvn clean compile'
                }
            }
        }
    }
}
