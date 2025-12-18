pipeline {
    agent any
    
    environment {
        DOCKER_IMAGE = "emnabenhdid/student-management"
    }
    
    stages {
        stage('📥 Checkout') {
            steps {
                echo '📥 Récupération du code depuis GitHub...'
                checkout scm
            }
        }
        
        stage('🔨 Compile') {
            steps {
                echo '🔨 Compilation du projet Maven...'
                dir('student-management') {
                    sh '/opt/maven/bin/mvn clean compile'
                }
            }
        }
        
        stage('📦 Package JAR') {
            steps {
                echo '📦 Création du fichier JAR (tests ignorés)...'
                dir('student-management') {
                    sh '/opt/maven/bin/mvn package -DskipTests'
                }
            }
        }
        
        stage('✅ Verify JAR') {
            steps {
                echo '✅ Vérification du JAR créé...'
                dir('student-management') {
                    sh 'ls -lh target/*.jar'
                }
            }
        }
        
        stage('🐳 Build Docker Image') {
            steps {
                echo '🐳 Construction de l\'image Docker...'
                dir('student-management') {
                    script {
                        sh """
                            docker build -t ${DOCKER_IMAGE}:${BUILD_NUMBER} .
                            docker tag ${DOCKER_IMAGE}:${BUILD_NUMBER} ${DOCKER_IMAGE}:latest
                        """
                    }
                }
            }
        }
        
        stage('🔍 Verify Image') {
            steps {
                echo '🔍 Vérification de l\'image Docker...'
                sh "docker images | grep ${DOCKER_IMAGE}"
            }
        }
        
        stage('☁️ Push to Docker Hub') {
            steps {
                echo '☁️ Push de l\'image vers Docker Hub...'
                withCredentials([usernamePassword(
                    credentialsId: 'docker-hub-credentials',
                    usernameVariable: 'DOCKER_USER',
                    passwordVariable: 'DOCKER_PASS'
                )]) {
                    sh '''
                        echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin
                        docker push ${DOCKER_IMAGE}:${BUILD_NUMBER}
                        docker push ${DOCKER_IMAGE}:latest
                        docker logout
                    '''
                }
            }
        }
        
        stage('🚀 Deploy') {
            steps {
                echo '🚀 Déploiement de l\'application...'
                sh """
                    # Arrêter l'ancienne version
                    docker stop student-app || true
                    docker rm student-app || true
                    
                    # Lancer la nouvelle version
                    docker run -d \
                        --name student-app \
                        -p 8081:8080 \
                        --restart unless-stopped \
                        ${DOCKER_IMAGE}:latest
                    
                    # Attendre le démarrage
                    sleep 10
                """
            }
        }
        
        stage('🔍 Health Check') {
            steps {
                echo '🔍 Vérification que l\'application tourne...'
                sh '''
                    docker ps | grep student-app
                    docker logs student-app --tail 20
                '''
            }
        }
    }
    
    post {
        success {
            echo '✅ ✅ ✅ PIPELINE RÉUSSI ! ✅ ✅ ✅'
            echo "🐳 Image Docker : ${DOCKER_IMAGE}:${BUILD_NUMBER}"
            echo "🌐 Application accessible sur : http://localhost:8081"
            archiveArtifacts artifacts: 'student-management/target/*.jar', fingerprint: true
        }
        failure {
            echo '❌ ❌ ❌ PIPELINE ÉCHOUÉ ! ❌ ❌ ❌'
            echo '📋 Vérifiez les logs ci-dessus'
        }
        always {
            echo '🧹 Nettoyage des images Docker inutilisées...'
            sh 'docker system prune -f || true'
        }
    }
}
