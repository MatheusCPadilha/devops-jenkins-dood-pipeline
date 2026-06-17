# 🚀 Automação de CI/CD: Docker outside of Docker (DooD) com Jenkins

Este laboratório prático demonstra a criação de uma esteira automatizada (Pipeline as Code) utilizando Jenkins rodando de forma conteinerizada, com capacidade de interagir com o daemon do Docker do host (Arch Linux) para provisionar, testar e destruir ambientes.

## 🛠️ Stack Tecnológico
* **Linux (CachyOS/Arch):** Sistema operacional host e base de comandos de shell.
* **Docker:** Gerenciamento de imagens, volumes e isolamento de rede.
* **Jenkins:** Orquestração da esteira de integração contínua (CI).
* **Groovy / Declarative Pipeline:** Estruturação do fluxo em `Jenkinsfile`.

## ⚙️ O Desafio Resolvido
O Jenkins oficial roda isolado e não possui acesso ao motor do Docker. Para este projeto, foi construída uma **imagem customizada do Jenkins** injetando o `docker-ce-cli` e mapeando o `/var/run/docker.sock`. Isso permitiu ao Jenkins criar novos containers a partir de si mesmo (DooD) com tratamento seguro de permissões.

## 🔄 Fluxo do Pipeline (Stages)
1. **Limpeza:** Tratamento de erros (`|| true`) para remover instâncias residuais.
2. **Build:** Criação dinâmica de um `Dockerfile` e empacotamento de um servidor web Nginx.
3. **Deploy:** Subida do container em background expondo a porta de serviço.
4. **Validação Automática:** Extração dinâmica do IP interno do container (`docker inspect`) e teste de conectividade (`curl`) para garantir o status HTTP 200 OK.
