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
                sh 'mvn clean package -DskipTests'
            }
        }

        stage('Verificar WAR generado') {
            steps {
                sh '''
                echo "Contenido de target:"
                ls -lah target
                '''
            }
        }

        stage('Eliminar Imagen Anterior') {
            steps {
                sh '''
                docker rmi ${IMAGE_NAME}:latest || true
                '''
            }
        }

        stage('Construir Imagen Docker') {
            steps {
                sh '''
                docker build -t ${IMAGE_NAME}:latest .
                '''
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

        stage('Verificar Despliegue') {
            steps {
                sh '''
                echo "===== CONTENEDORES ====="
                docker ps

                echo ""
                echo "===== IMAGENES ====="
                docker images
                '''
            }
        }
    }

    post {

        success {
            echo '============================================='
            echo 'PIPELINE FINALIZADO CORRECTAMENTE'
            echo 'La aplicación fue desplegada en Docker.'
            echo 'Swagger: http://IP_PUBLICA_EC2:9090/vehiculosBuild/swagger-ui/index.html'
            echo '============================================='
        }

        failure {
            echo '============================================='
            echo 'PIPELINE FALLÓ'
            echo 'Revisar el Console Output.'
            echo '============================================='
        }

        always {
            sh 'docker ps -a'
        }
    }
}