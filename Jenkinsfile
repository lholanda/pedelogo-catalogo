pipeline {

    agent any

    stages {
        stage('Git Checkout to Master'){
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

        stage('Deploy Kubernetes'){
            agent {
                kubernetes {
                    cloud 'kubernetes'
                }
            }
            environment {
                tag_version= "v${env.BUILD_ID}"
            }
            steps {
                script{
                    sh 'sed -i "s/{{tag}}/$tag_version/g" ./k8s/api/deployment.yaml'
                    sh 'cat ./k8s/api/deployment.yaml'
                    KubernetesDeploy(configs: '**/k8s/**', kubeconfigId : 'kubeconfig')
                    }
                }
            }
        }

    }


