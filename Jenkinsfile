pipeline {
    agent any

    stages {

        stage('Verificar herramientas') {
            steps {
                sh 'java -version'
                sh 'git --version'
                sh 'mvn -version'
                sh 'docker --version'
            }
        }

        stage('Compilar proyecto') {
            steps {
                sh 'mvn clean package -DskipTests'
            }
        }

        stage('Construir imagen Docker') {
            steps {
                sh 'docker build -t vehiculos-api:latest .'
            }
        }

        stage('Eliminar contenedor anterior') {
            steps {
                sh '''
                docker stop vehiculos-api || true
                docker rm vehiculos-api || true
                '''
            }
        }

        stage('Desplegar contenedor') {
            steps {
                sh '''
                docker run -d \
                  --name vehiculos-api \
                  -p 9090:8080 \
                  vehiculos-api:latest
                '''
            }
        }

    }

    post {
        success {
            echo 'Pipeline ejecutado correctamente.'
        }

        failure {
            echo 'El pipeline falló.'
        }
    }
}