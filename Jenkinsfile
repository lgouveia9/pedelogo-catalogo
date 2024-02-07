pipeline {
    agent any

    stages {
        stage('Checkout Source') {
           steps{
            git url:'https://github.com/lgouveia9/pedelogo-catalogo.git', branch:'main'
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
