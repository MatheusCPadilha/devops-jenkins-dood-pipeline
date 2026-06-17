pipeline {
    agent any

    stages {
        stage('Limpeza de Resíduos') {
            steps {
                echo '=== Derrubando containers antigos para não dar conflito ==='
                sh 'docker rm -f app-teste-jenkins || true'
            }
        }

        stage('Construindo a Infra (Docker Build)') {
            steps {
                echo '=== Criando um arquivo de site e gerando a Imagem Docker ==='
                sh '''
                    echo "<h1>Bolo Assado pelo Jenkins no CachyOS!</h1>" > index.html
                    echo "FROM nginx:alpine" > Dockerfile
                    echo "COPY index.html /usr/share/nginx/html/" >> Dockerfile
                    docker build -t meu-app-nginx:latest .
                '''
            }
        }
        
        stage('Disparando o Deploy de Teste') {
            steps {
                echo '=== Subindo o container na porta 8081 ==='
                sh 'docker run -d -p 8081:80 --name app-teste-jenkins meu-app-nginx:latest'
            }
        }

	stage('Troubleshooting & Validação') {
            steps {
                echo '=== Testando se a página responde (Mapeamento de IP Dinâmico) ==='
                sh '''
                    # Pede pro Docker o IP interno exato do container que acabou de subir
                    APP_IP=$(docker inspect -f '{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}' app-teste-jenkins)
                    
                    echo "O IP interno do container é: $APP_IP"
                    echo "Disparando o teste de rede..."
                    
                    # Faz o teste na porta 80 original direto no IP do container
                    curl -I http://$APP_IP:80
                '''
            }
        }
}
