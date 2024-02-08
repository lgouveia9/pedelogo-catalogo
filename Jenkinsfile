pipeline {
    agent any

    stages {
        stage('Checkout Source') {
           steps{
            git url:'https://github.com/lgouveia9/pedelogo-catalogo.git', branch:'main'
           }
        }

        stage('Instalar Docker') {
            steps {
                script {
                    // Atualiza os repositórios do pacote e instala os pré-requisitos
                    sh 'sudo apt-get update'
                    sh 'sudo apt-get install -y apt-transport-https ca-certificates curl software-properties-common'
                    
                    // Adiciona a chave GPG oficial do Docker
                    sh 'curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -'
                    
                    // Configura o repositório estável do Docker
                    sh 'sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"'
                    
                    // Atualiza os repositórios do pacote novamente e instala o Docker
                    sh 'sudo apt-get update'
                    sh 'sudo apt-get install -y docker-ce'
                }
            }
        }

        stage('Build Image') {
           steps {
               script {
                   dockerapp = docker.build("lgouveia/pedelogocatalogo:latest",
                     '-f /opt/jenkins/pedelogo-catalogo/src/PedeLogo.Catalogo.Api/Dockerfile .')
               }
           }
        }

        stage('Push Image') {
            steps {
               script {
                   docker.withRegistry('https://registry.hub.docker.com', 'dockerhub') {
                    dockerapp.push("latest")
                   }
               }
           }
        }

        stage('Docker Run') {
            steps {
                sh 'docker container rm pedelogo -f'
                sh 'docker run -d -p 8090:80 --name pedelogo lgouveia/pedelogocatalogo:latest'
            }
        }     
        
    }
}
