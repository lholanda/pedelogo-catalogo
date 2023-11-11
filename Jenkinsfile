pipeline{
    agent any

    stages{
        /* CI */
        stage('Checkout Source'){
            steps {
                git url:'https://github.com/lholanda/pedelogo-catalogo.git', branch:'master' 
            }
        }
        stage('Build Image'){
            steps {
               script {
                    dockerapp = docker.build("lholanda/lh-produto:lh${env.BUILD_ID}",'-f ./src/PedeLogo.Catalogo.Api/Dockerfile .')
               } 
            }
        }
        stage('Push Image'){
            steps {
                script {
                      docker.withRegistry('https://registry.hub.docker.com', 'dockerhub') {
                      dockerapp.push("lh${env.BUILD_ID}")
                      dockerapp.push('latest')
                   }
                }
            }
        }

        /* CD */
        stage('Deploy Kubernetes'){
            agent {
                kubernetes {
                    cloud 'kubernetes'
                }
            }

            environment {
                tag_version = "lh${env.BUILD_ID}"
            }
            
            steps {
                
                sh 'sed -i "s/{{tag}}/$tag_version/g" ./k8s/api/deployment.yaml'
                sh 'cat ./k8s/api/deployment.yaml'
                withKubeConfig([credentialsId:'kube'
                               ]){
                    sh './kubectl apply -f ./k8s/ -R'
                }
                
            }
        }

    }
}