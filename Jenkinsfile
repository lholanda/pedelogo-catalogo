pipeline{
    agent any

    stages{
        stage('Checkout Source'){
            stage {
                steps {
                   git url:'https://github.com/lholanda/pedelogo-catalogo.git', branch:'master' 
                }
            }
        }

        stage('Build Image'){
            steps {
               script {
                    dockerapp = docker.build("lholanda/pedelogo-catalogo:v${env.BUILD_ID}",'-f ./src/PedeLogo.Catalogo.Api/Dockerfile .')
               } 
            }
        }

        stage('Push Image'){
            steps {
                script {
                    echo 'My pipeline test 1'
                }
            }
        }

    }
}