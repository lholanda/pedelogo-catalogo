pipeline{
    agent any

    environment {
       TAG_VERSION = "v${env.BUILD_ID}.0"  /*  estava dando erro pois eu nao estava subuindo com .0 para o Deploy*/
       IMAGE_NAME  = "lholanda/api-produto${TAG_VERSION}"
       OPTION_BUILD_IMAGE = "--no-cache"
    }


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
                    dockerapp = docker.build("${IMAGE_NAME}",'${OPTION_BUILD_IMAGE} -f ./src/PedeLogo.Catalogo.Api/Dockerfile .')
               } 
            }
        }
        stage('Push Image'){
            steps {
                script {
                      docker.withRegistry('https://registry.hub.docker.com', 'dockerhub') {
                      dockerapp.push("${TAG_VERSION}")
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

            steps {
                sh 'sed -i "s/{{tag}}/${IMAGE_NAME}/g" ./k8s/api/deployment.yaml'
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