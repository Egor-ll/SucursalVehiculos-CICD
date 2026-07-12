pipeline {
    agent any

    environment {
        IMAGE_NAME = "vehiculos-api"
        CONTAINER_NAME = "vehiculos-api"
    }

    stages {

        stage('Verificar entorno') {
            steps {
                sh 'echo "===== JAVA ====="'
                sh 'java -version'

                sh 'echo "===== MAVEN ====="'
                sh 'mvn -version'

                sh 'echo "===== GIT ====="'
                sh 'git --version'

                sh 'echo "===== DOCKER ====="'
                sh 'docker --version'
            }
        }

        stage('Compilar Proyecto') {
            steps {
                sh 'mvn clean package -DskipTests=true'
            }
        }

        stage('Verificar WAR generado') {
            steps {
                sh 'echo "Contenido carpeta target:"'
                sh 'ls -lah target'
            }
        }

        stage('Construir Imagen Docker') {
            steps {
                sh 'docker build -t ${IMAGE_NAME}:latest .'
            }
        }

        stage('Eliminar Contenedor Anterior') {
            steps {
                sh '''
                docker stop ${CONTAINER_NAME} || true
                docker rm ${CONTAINER_NAME} || true
                '''
            }
        }

        stage('Desplegar Contenedor') {
            steps {
                sh '''
                docker run -d \
                --name ${CONTAINER_NAME} \
                -p 9090:8080 \
                ${IMAGE_NAME}:latest
                '''
            }
        }

        stage('Verificar Contenedor') {
            steps {
                sh 'docker ps'
            }
        }

    }

    post {

        success {
            echo '======================================='
            echo ' Pipeline ejecutado correctamente'
            echo ' Aplicación desplegada en Docker'
            echo '======================================='
        }

        failure {
            echo '======================================='
            echo ' El Pipeline falló'
            echo ' Revisar Console Output'
            echo '======================================='
        }

        always {
            sh 'docker images'
        }

    }
}