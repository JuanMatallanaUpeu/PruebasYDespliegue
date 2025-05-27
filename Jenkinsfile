// Jenkinsfile para un proyecto Java con Maven y SonarQube
pipeline {
    agent any // Puedes especificar un agente más específico si tienes nodos esclavos

    tools {
        // Asegúrate de que Maven esté configurado en Jenkins en 'Manage Jenkins -> Global Tool Configuration'
        maven 'Maven 3.8.6' // Reemplaza con la versión de Maven que tengas configurada
    }

    stages {
        stage('Checkout') {
            steps {
                git 'https://github.com/tu_usuario/tu_repositorio.git' // Reemplaza con la URL de tu repositorio
            }
        }

        stage('Build') {
            steps {
                sh 'mvn clean install -DskipTests' // Compila el proyecto, saltando los tests inicialmente
            }
        }

        stage('Unit Tests') {
            steps {
                sh 'mvn test' // Ejecuta los tests unitarios
            }
        }

        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('tu_nombre_del_servidor_sonarqube') { // El nombre del servidor SonarQube configurado en Jenkins
                    sh "mvn org.sonarsource.scanner.maven:sonar-maven-plugin:3.9.0.2155:sonar -Dsonar.projectKey=YOUR_PROJECT_KEY -Dsonar.host.url=http://localhost:9000 -Dsonar.token=YOUR_SONARQUBE_TOKEN"
                    // Asegúrate de que el plugin de SonarQube Maven sea la versión correcta (o una más reciente)
                    // Reemplaza YOUR_PROJECT_KEY, YOUR_SONARQUBE_TOKEN y la URL de SonarQube
                }
            }
        }

        stage('SonarQube Quality Gate') {
            steps {
                script {
                    timeout(time: 5, unit: 'MINUTES') { // Espera hasta 5 minutos por el resultado del Quality Gate
                        def qg = waitForQualityGate()
                        if (qg.status != 'OK') {
                            error "Pipeline aborted due to Quality Gate failure: ${qg.status}"
                        }
                    }
                }
            }
        }
    }

    post {
        always {
            cleanWs() // Limpia el workspace de Jenkins al finalizar
        }
        failure {
            echo 'El pipeline falló. Revisa los logs para más detalles.'
        }
        success {
            echo 'El pipeline se ejecutó exitosamente.'
        }
    }
}