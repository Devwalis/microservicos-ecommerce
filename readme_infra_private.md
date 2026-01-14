# 📦 Documentação – Dockerização do Usuario Service

Este documento descreve **passo a passo** tudo o que foi feito até o momento para:

* Instalar e validar o Docker no Linux
* Buildar um microserviço Spring Boot (usuario-service)
* Criar uma imagem Docker da aplicação
* Subir e acessar um banco MariaDB localmente
* Acessar o banco via interface web (Adminer)
* Permitir acesso de containers Docker ao MariaDB
* Executar e testar o serviço conteinerizado

O objetivo é permitir que **qualquer pessoa** consiga reproduzir o ambiente local e acessar o serviço.

---

## 🖥️ Ambiente

* Sistema operacional: **Linux (Debian/Ubuntu-based)**
* Java: **Java 21+**
* Spring Boot: **4.x**
* Banco de dados: **MariaDB**
* Docker Engine + Docker Compose

---

## 1️⃣ Instalação do Docker

### Atualizar pacotes

```bash
sudo apt update
sudo apt upgrade -y
```

### Instalar dependências

```bash
sudo apt install ca-certificates curl gnupg lsb-release -y
```

### Adicionar chave e repositório Docker

```bash
sudo mkdir -p /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/debian/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg

echo "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/debian $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
```

### Instalar Docker

```bash
sudo apt update
sudo apt install docker-ce docker-ce-cli containerd.io docker-compose-plugin -y
```

### Permitir uso do Docker sem sudo

```bash
sudo usermod -aG docker $USER
newgrp docker
```

### Verificar instalação

```bash
docker --version
docker ps
```

---

## 2️⃣ Build do Projeto Spring Boot

Dentro da pasta do serviço:

```bash
cd usuario-service
```

### Gerar o JAR

```bash
./mvnw clean package -DskipTests
```

Resultado esperado:

```bash
target/usuario-service-0.0.1-SNAPSHOT.jar
```

---

## 3️⃣ Dockerfile do Serviço

O serviço foi dockerizado usando **multi-stage build**.

### Dockerfile

```dockerfile
# Stage 1 – build
FROM maven:3.9.9-eclipse-temurin-21 AS build
WORKDIR /app
COPY pom.xml .
COPY src ./src
RUN mvn clean package -DskipTests

# Stage 2 – runtime
FROM eclipse-temurin:21-jre
WORKDIR /app
COPY --from=build /app/target/*.jar app.jar
EXPOSE 8083
ENTRYPOINT ["java","-jar","app.jar"]
```

---

## 4️⃣ Build da Imagem Docker

```bash
docker build -t usuario-service:1.0 .
```

Verificar imagem criada:

```bash
docker images
```

---

## 5️⃣ Subindo o Banco MariaDB Local

### Instalação do MariaDB

```bash
sudo apt install mariadb-server mariadb-client -y
```

### Verificar serviço

```bash
sudo systemctl status mariadb
```

### Ajustar bind-address

Arquivo:

```bash
sudo nano /etc/mysql/mariadb.conf.d/50-server.cnf
```

Alterar:

```ini
bind-address = 0.0.0.0
```

Reiniciar serviço:

```bash
sudo systemctl restart mariadb
```

Verificar porta:

```bash
sudo ss -lntp | grep 3306
```

---

## 6️⃣ Criação do Banco e Usuário

Entrar no MariaDB:

```bash
sudo mariadb -u root -p
```

Criar banco:

```sql
CREATE DATABASE usuario_db;
```

Criar usuário com acesso remoto:

```sql
CREATE USER 'usuario_user'@'%' IDENTIFIED BY 'sodalita-usuario';
GRANT ALL PRIVILEGES ON usuario_db.* TO 'usuario_user'@'%';
FLUSH PRIVILEGES;
```

---

## 7️⃣ Interface Web para o Banco (Adminer)

Subir Adminer via Docker:

```bash
docker run -d \
  --name adminer \
  -p 8081:8080 \
  adminer
```

Acessar no navegador:

```
http://localhost:8081
```

Credenciais:

* Sistema: MariaDB / MySQL
* Servidor: 172.17.0.1
* Usuário: usuario_user
* Senha: sodalita-usuario
* Base: usuario_db

---

## 8️⃣ Variáveis de Ambiente do Serviço

Antes de rodar o container:

```bash
export SPRING_DATASOURCE_URL=jdbc:mariadb://localhost:3306/usuario_db
export SPRING_DATASOURCE_USERNAME=usuario_user
export SPRING_DATASOURCE_PASSWORD=sodalita-usuario
export SERVER_PORT=8083
```

Essas variáveis são consumidas no `application.properties`:

```properties
server.port=${SERVER_PORT}
spring.datasource.url=${SPRING_DATASOURCE_URL}
spring.datasource.username=${SPRING_DATASOURCE_USERNAME}
spring.datasource.password=${SPRING_DATASOURCE_PASSWORD}
```

---

## 9️⃣ Executando o Container do Serviço

```bash
docker run -d \
  -p 8083:8083 \
  --name usuario-service \
  -e SPRING_DATASOURCE_URL=jdbc:mariadb://172.17.0.1:3306/usuario_db \
  -e SPRING_DATASOURCE_USERNAME=usuario_user \
  -e SPRING_DATASOURCE_PASSWORD=sodalita-usuario \
  -e SERVER_PORT=8083 \
  usuario-service:1.0
```

Verificar:

```bash
docker ps
```

---

## 🔎 Como Outras Pessoas Acessam o Serviço

* API disponível em:

```
http://<IP_DA_MÁQUINA>:8083
```

* Banco via Adminer:

```
http://<IP_DA_MÁQUINA>:8081
```

⚠️ A porta precisa estar liberada no firewall.

---

## ✅ Estado Atual do Projeto

✔ Serviço conteinerizado
✔ Banco funcional
✔ Interface web para banco
✔ Variáveis de ambiente externalizadas
✔ Ambiente reproduzível

---

## 🚀 Próximos Passos (não executados ainda)

* Criar `docker-compose.yml`
* Containerizar MariaDB
* Criar rede Docker
* Padronizar para múltiplos microserviços
* Preparar para deploy em nuvem

---

📌 **Este README representa o marco inicial do ambiente Docker do projeto de microserviços.**
