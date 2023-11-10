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

###### DOCKER INSTALATION ########
curl -fsSl https://get.docker.com | bash 
usermod -aG docker jenkins

1) Security -
    agents - random - para o k8s consiga funcionar 
2) credenciais
    dockerhub e kube
3) Clouds
    Credentials
    Jenkins URL http://<IP>:8080
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
4) Projeto para subir
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

