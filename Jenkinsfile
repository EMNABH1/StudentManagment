pipeline {
    agent any
    
    stages {
        stage('📥 Checkout') {
            steps {
                echo '📥 Récupération du code depuis GitHub...'
                checkout scm
            }
        }
        
        stage('🔨 Compile') {
            steps {
                echo '🔨 Compilation du projet...'
                dir('student-management') {
                    sh '/opt/maven/bin/mvn clean compile'
                }
            }
        }
        
        stage('🧪 Tests') {
            steps {
                echo '🧪 Exécution des tests...'
                dir('student-management') {
                    sh '/opt/maven/bin/mvn test'
                }
            }
            post {
                always {
                    junit 'student-management/target/surefire-reports/*.xml'
                }
            }
        }
        
        stage('📦 Package JAR') {
            steps {
                echo '📦 Création du fichier JAR...'
                dir('student-management') {
                    sh '/opt/maven/bin/mvn package -DskipTests'
                }
            }
        }
        
        stage('🐳 Build Docker Image') {
            steps {
                echo '🐳 Construction de l\'image Docker...'
                dir('student-management') {
                    script {
                        sh """
                            docker build -t emnabenhdid/student-management:${BUILD_NUMBER} .
                            docker tag emnabenhdid/student-management:${BUILD_NUMBER} emnabenhdid/student-management:latest
                        """
                    }
                }
            }
        }
        
        stage('☁️ Push Docker Hub') {
            steps {
                echo '☁️ Push vers Docker Hub...'
                withCredentials([usernamePassword(
                    credentialsId: 'docker-hub-credentials',
                    usernameVariable: 'DOCKER_USER',
                    passwordVariable: 'DOCKER_PASS'
                )]) {
                    sh '''
                        echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin
                        docker push emnabenhdid/student-management:${BUILD_NUMBER}
                        docker push emnabenhdid/student-management:latest
                    '''
                }
            }
        }
        
        stage('🚀 Deploy') {
            steps {
                echo '🚀 Déploiement de l\'application...'
                sh """
                    docker stop student-app || true
                    docker rm student-app || true
                    docker run -d \
                        --name student-app \
                        -p 8081:8080 \
                        emnabenhdid/student-management:latest
                """
            }
        }
    }
    
    post {
        success {
            echo '✅ ✅ ✅ PIPELINE RÉUSSI ! ✅ ✅ ✅'
            echo "🐳 Image : emnabenhdid/student-management:${BUILD_NUMBER}"
            archiveArtifacts artifacts: 'student-management/target/*.jar', fingerprint: true
        }
        failure {
            echo '❌ ❌ ❌ PIPELINE ÉCHOUÉ ! ❌ ❌ ❌'
        }
        always {
            echo '🧹 Nettoyage...'
            sh 'docker system prune -f || true'
        }
    }
}
