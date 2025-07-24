pipeline {
    agent any

    tools {
        nodejs "nodejs24"
        dockerTool "dockertools" 
    }

    stages {
        stage('Instalar dependencias') {
            steps {
                sh 'npm install'
            }
        }

        stage('Preparar entorno de pruebas') {
            steps {
                // Dar permisos de ejecución a los binarios de node_modules
                sh 'chmod +x node_modules/.bin/jest'
            }
        }

        stage('Ejecutar tests') {
            steps {
                // Puedes usar --ci --runInBand si es necesario para entornos como Jenkins
                sh 'npm test -- --ci --runInBand'
            }
        }

        stage('Construir Imagen Docker') {
            when {
                expression { currentBuild.result == null || currentBuild.result == 'SUCCESS' }
            }
            steps {
                sh 'docker build -t hola-mundo-node:latest .'
            }
        }

        stage('Ejecutar Contenedor Node.js') {
            when {
                expression { currentBuild.result == null || currentBuild.result == 'SUCCESS' }
            }
            steps {
                sh '''
                    docker stop hola-mundo-node || true
                    docker rm hola-mundo-node || true
                    docker run -d --name hola-mundo-node -p 3000:3000 hola-mundo-node:latest
                '''
            }
        }
    }
}
