# pedelogo-catalogo

# Projeto pedelogo-catalogo

### Objetivo
O projeto pedelogo-catalogo é uma aplicação escrita em C# e tem como objetivo ser uma aplicação de exemplo pra trabalhar com o uso de containers.

### Configuração
Pra configurar a aplicação, é preciso ter um banco de dados Mongo e pra definir o acesso ao banco, configure as variáveis de ambiente abaixo:

Mongo__DataBase => Nome do database

Mongo__Host => Host do MongoDB

MONGODB_PORT => Posta de acesso ao MongoDB

Mongo__User => Usuário do MongoDB

Mongo__Password => Senha do MongoDB

Mongo__Port => Porta do MongoDB

### ------------ Configuracao do Jenkins

acesso ao servidor
ssh root@104.248.61.44 -i ~/.ssh/aula-jenkins

###### DOCKER INSTALATION ######## --- JA INSTALA TUDO O QUE PRECISA PARA O DOCKER

sudo apt update
sudo curl -fsSl https://get.docker.com | bash 
sudo usermod -aG docker 

###### JAVA INSTALATION ########

sudo apt update && sudo apt install openjdk-17-jre && java -version

###### JENKINS INSTALATION ########

curl -fsSL https://pkg.jenkins.io/debian-stable/jenkins.io-2023.key | sudo tee /usr/share/keyrings/jenkins-keyring.asc > /dev/null

echo deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc] https://pkg.jenkins.io/debian-stable binary/ | sudo tee /etc/apt/sources.list.d/jenkins.list > /dev/null

sudo apt-get update && sudo apt-get install jenkins
sudo usermod -aG docker jenkins

--- monitoramento 
systemctl cat jenkins

sudo systemctl status jenkins

--- para inicializar o jenkins 
sudo cat /var/lib/jenkins/secrets/initialAdminPassword

-----------------------------------------------------------

1) Security -
    agents - random - para o k8s consiga funcionar 
2) credenciais
    dockerhub e kube
3) Clouds
    Credentials
    Jenkins URL http://104.248.61.44:8080  ssh root@104.248.61.44 -i ~/.ssh/aula-jenkins
    Pod Label 
        key : jenkins
        value: slave
        Pod Retention -> On failure
        Pod template
            Name: pod-template
            Labels: kubepods
            Containers
                Name: jnlp
                Docker image
                    jenkins/jnlp-slave:latest
                    working directory - apaga
                    Command to run - apaga
                    [ x ] Allocate pseudo-TTY
        SAVE

4) Configurando o Kubernetes
 DO - create a kubernetes
 nyc1
 fixed size
 Node pool name : default  
 Node plan: 24$

5) Job
PIPELINE
git project : https://github.com/lholanda/pedelogo-catalogo.git
nao usa credencial - deixar em branco para nao dar erro

5) Projeto para subir
    fork do projeto ou projeto que queira subir

    * para testar:
    cd ~/projects/jornadadevops/jornada/jenkins/app/pedelogo-catalogo

    * comando para criar Imagem
    docker build -t lholanda/api-produto:versao-teste -f ./src/PedeLogo.Catalogo.Api/Dockerfile .
    
    cd ~/projects/jornadadevops/jornada/jenkins/app/pedelogo-catalogo$
    * comando para criar Imagem
    docker build -t lholanda/pedelogo-catalogo:v3.0.0 -f ./pedelogo-catalogo/src/PedeLogo.Catalogo.Api/Dockerfile ./pedelogo-catalogo
    * subir a imagem para o Dockerhub
    docker push lholanda/pedelogo-catalogo:v3.0.0

    ~/projects/jornadadevops/jornada/jenkins/app$ 
    docker build -t lholanda/api-produto:versao-teste -f ./pedelogo-catalogo/src/PedeLogo.Catalogo.Api/Dockerfile ./pedelogo-catalogo

DockerHub / GitHub (iguais)
lholanda/ZAXila#002

5) adicionar os manifestos do projeto


aqui IMAGEM
***********************************************

6) criar os pods, services, secrets , etc...
    no diretorio:
        cd ~/projects/jornadadevops/jornada/jenkins/app/pedelogo-catalogo
    rodar :
        kubectl apply -f ./k8s/ -R

    para excluir rodar :
        kubectl delete -f ./k8s/ -R

    para consultar:
        kubectl get all

7) Criando tudo atraves da pipeline do jenkins



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



/* CD */

/*kuberneteDeploy(configs: '**/k8s/**', kubeconfigId: 'kubeconfig')*/



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

***----------------- subi na mao fora do Jenkin no kubernetes da DO

k get all
NAME                                      READY   STATUS    RESTARTS   AGE
pod/api-deployment-68bf548875-46jvw       1/1     Running   0          3m48s
pod/mongodb-deployment-58669bdbf9-b6tvr   1/1     Running   0          3m47s

NAME                      TYPE           CLUSTER-IP       EXTERNAL-IP       PORT(S)        AGE
service/api-service       LoadBalancer   10.245.133.32    159.203.159.107   80:32006/TCP   3m48s
service/kubernetes        ClusterIP      10.245.0.1       <none>            443/TCP        5h45m
service/mongodb-service   ClusterIP      10.245.128.149   <none>            27017/TCP      3m47s

NAME                                 READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/api-deployment       1/1     1            1           3m48s
deployment.apps/mongodb-deployment   1/1     1            1           3m47s

NAME                                            DESIRED   CURRENT   READY   AGE
replicaset.apps/api-deployment-68bf548875       1         1         1       3m49s
replicaset.apps/mongodb-deployment-58669bdbf9   1         1         1       3m48s

uso external IP 
http://159.203.159.107/swagger/index.html


### Pipeline Result


+ docker build -t lholanda/api-produto:v42.0 -f ./src/PedeLogo.Catalogo.Api/Dockerfile .

