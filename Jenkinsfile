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

        stage('Push Image') {
            steps {
                script {
                    docker.withRegistry('https://registry.hub.docker.com', 'docker-hub') {              
                    dockerapp.push('latest')
                    dockerapp.push("${env.BUILD_ID}")
                    }             
                }
            }
        }

        stage('Deploy k8s') {
            agent { 
                kubernetes { 
                    cloud 'kubernetes'
                }
            }
            environment {
                tag_version = "${env.BUILD_ID}"
            }
            steps {
                script {
                    sh 'sed -i "s/{{tag}}/$tag_version/g" ./k8s/api/deployment.yaml'              
                    sh 'cat ./k8s/api/deployment.yaml'
                    kubernetesDeploy(configs: '**/k8s/**', kubeconfigId: 'kubeconfig')
                    kubernetesDeploy(configs: '**/nginx/**', kubeconfigId: 'kubeconfig')
                    }             
                }
            }
        
    }
}

