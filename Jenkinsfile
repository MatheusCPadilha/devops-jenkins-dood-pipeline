pipeline {
    agent any

    environment {
        // Variaveis de ambiente: Define o nome da imagem apontando para o seu repo na nuvem
        DOCKER_IMAGE = 'matheuspad12333/meu-app-nginx:latest'
        CONTAINER_NAME = 'app-teste-jenkins'
    }

    stages {
        stage('Limpeza de Resíduos') {
            steps {
                echo '=== Derrubando containers antigos para não dar conflito ==='
                sh "docker rm -f ${CONTAINER_NAME} || true"
            }
        }

        stage('Construindo a Infra (Docker Build)') {
            steps {
                echo '=== Criando arquivo do site e gerando a Imagem Docker ==='
                sh """
                    echo "<h1>Bolo Assado pelo Jenkins no CachyOS e enviado pro Docker Hub!</h1>" > index.html
                    echo "FROM nginx:alpine" > Dockerfile
                    echo "COPY index.html /usr/share/nginx/html/" >> Dockerfile
                """
                // Build usando a variavel com o seu usuario do Docker Hub
                sh "docker build -t ${DOCKER_IMAGE} ."
            }
        }

        stage('Push para o Docker Hub') {
            steps {
                echo '=== Autenticando e enviando para a Nuvem ==='
                // Puxa o seu token do cofre secreto do Jenkins
                withCredentials([usernamePassword(credentialsId: 'dockerhub-creds', passwordVariable: 'DOCKER_PASS', usernameVariable: 'DOCKER_USER')]) {
                    sh 'echo "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin'
                    sh "docker push ${DOCKER_IMAGE}"
                }
            }
        }

        stage('Disparando o Deploy de Teste') {
            steps {
                echo '=== Subindo o container na porta 8081 ==='
                sh "docker run -d -p 8081:80 --name ${CONTAINER_NAME} ${DOCKER_IMAGE}"
            }
        }

        stage('Troubleshooting & Validação') {
            steps {
                echo '=== Testando se a página responde (Mapeamento de IP Dinâmico) ==='
                sh '''
                    # Pede pro Docker o IP interno exato do container
                    APP_IP=$(docker inspect -f '{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}' app-teste-jenkins)
                    
                    echo "O IP interno do container é: $APP_IP"
                    echo "Disparando o teste de rede..."
                    
                    # Faz o teste direto no IP do container
                    curl -I http://$APP_IP:80
                '''
            }
        }
    }
}
