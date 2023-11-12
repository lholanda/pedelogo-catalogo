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
                    dockerapp = docker.build("lholanda/api-produto:v${env.BUILD_ID}.0",'-f ./src/PedeLogo.Catalogo.Api/Dockerfile .')
               } 
            }
        }
        stage('Push Image'){
            steps {
                script {
                      docker.withRegistry('https://registry.hub.docker.com', 'dockerhub') {
                      dockerapp.push("v${env.BUILD_ID}.0")
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
                tag_version = "v${env.BUILD_ID}"
            }

            steps {
                
                sh 'sed -i "s/{{tag}}/$tag_version/g" ./k8s/api/deployment.yaml'
                sh 'cat ./k8s/api/deployment.yaml'
                withKubeConfig([credentialsId:'kube'
                               ]){
                    sh 'curl -LO "https://storage.googleapis.com/kubernetes-release/release/v1.20.5/bin/linux/amd64/kubectl"'  
                    sh 'chmod u+x ./kubectl'  
                    sh './kubectl apply -f ./k8s/ -R'
                }
                
            }
        }

    }
}