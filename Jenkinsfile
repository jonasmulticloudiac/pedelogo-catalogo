pipeline {
    agent any 

    stages {

        stage('Check-out Source') {
            steps {
                git url:'https://github.com/jonasmulticloudiac/pedelogo-catalogo.git', branch:'main'
            }
        }

        stage('Build Image') {
            steps {
                script {
                    dockerapp = docker.build("jonasmulticloudiac/api-produto:${env.BUILD_ID}",
                    '-f ./src/PedeLogo.Catalogo.Api/Dockerfile .')
                }
            }
        }

        stage('Build Image') {
            steps {
                script {
                    docker.withRegistry('https://registry.hub.docker.com', 'docker-hub') {              
                    dockerapp.push('latest')
                    dockerapp.push("${env.BUILD_ID}")
                    }             
                }
            }
        }
    }
}

