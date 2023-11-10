pipeline {
    agent any
    stages {
        stage('Checkout Source'){
            steps {
                git url:'https://github.com/lholanda/pedelogo-catalogo.git', branch:'master'
            }
        }
        stage('Build Image'){
            steps {
                script {
                    dockerapp = docker.build("lholanda/api-produto:v${env.BUILD_ID}",
                    '-f ./src/PedeLogo.Catalogo.Api/Dockerfile .')
                }    
            }
        }

        stage('Push Image'){
            steps {
                script{
                    docker.withRegistry('https://registry.hub.docker.com', 'dockerhub') {
                        dockerapp.push('latest')
                        dockerapp.push("v${env.BUILD_ID}")
                    }
                }
            }
        }
    }


}