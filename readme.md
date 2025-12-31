# Sistema de Microsserviços – Java & AWS (Projeto de Estudos)

Este repositório contém um **sistema de microsserviços desenvolvido para fins de estudo**, aplicando **boas práticas profissionais de back-end, cloud e DevOps**, com foco em **Java, Spring Boot, Docker e AWS**.

O projeto foi pensado para rodar inicialmente em **EC2**, utilizando **Docker**, com possibilidade de **testes em Kubernetes**, sempre priorizando o **uso do Free Tier da AWS**.

---

## 📌 Objetivo do Projeto

- Estudar **arquitetura de microsserviços**
- Aplicar **boas práticas de back-end**
- Simular um **ambiente real de deploy na AWS**
- Trabalhar com **Docker, CI/CD e versionamento**
- Preparar base para testes com **Kubernetes**
- Utilizar **configurações seguras** (sem dados sensíveis no repositório)

---

## 🧱 Arquitetura

O sistema é composto por **microsserviços independentes**, cada um com:

- Código isolado
- Configurações próprias
- Porta dedicada
- Controle individual de dependências
- Arquivos sensíveis ignorados pelo Git

### Microsserviços

| Serviço | Descrição | Porta |
|-------|---------|------|
| compras-service | Gestão de compras | 8080 |
| pedidos-service | Gestão de pedidos | 8081 |
| produtos-service | Gestão de produtos | 8082 |
| usuarios-service | Gestão de usuários | 8083 |

---

## 🛠️ Tecnologias Utilizadas

### Back-end
- **Java 21**
- **Spring Boot**
- **Maven**
- **Spring Data JPA**
- **Spring Security (planejado)**

### Banco de Dados
- **MySQL**
- **MariaDB**

### Infraestrutura & DevOps
- **Docker**
- **Docker Compose**
- **AWS EC2**
- **Kubernetes (ambiente de testes)**
- **Git / GitHub**
- **CI/CD (em planejamento)**

---

## 🔐 Segurança e Boas Práticas

- Arquivos sensíveis (`application.properties`, `.env`) **não são versionados**
- Uso de `.gitignore` e `.gitattributes` em nível de serviço e raiz
- Configurações externas via **variáveis de ambiente**
- Separação clara entre código e configuração
- Padrões de commit e organização de repositório

---

## ▶️ Como Executar (Modo Desenvolvimento)

### Pré-requisitos
- Java 21
- Maven
- Docker e Docker Compose
- Git

### Executando um serviço individualmente

```bash
cd compras-service
mvn spring-boot:run
