# usuarios-service

Microsserviço responsável pela **gestão de usuários, autenticação e autorização** do sistema.  
Este serviço centraliza o controle de identidade e acesso, garantindo segurança e separação de responsabilidades dentro de uma **arquitetura de microsserviços**.

O projeto faz parte de um ambiente de estudos com foco em **boas práticas de back-end, cloud AWS, Docker e DevOps**, simulando cenários reais de produção.

---

## 📌 Responsabilidades

Este serviço é responsável por:

### 👤 Gestão de Usuários
- Cadastro de usuários do tipo:
  - Cliente
  - Vendedor
  - Administrador
- Consulta de dados do usuário
- Atualização de informações cadastrais
- Ativação e desativação de usuários

### 🔐 Autenticação e Autorização
- Autenticação de usuários (login)
- Validação de credenciais
- Autorização baseada em perfis (roles)
- Emissão de tokens JWT
- Validação de tokens JWT para comunicação entre serviços

### 🛡️ Segurança
- Armazenamento seguro de senhas (hash)
- Controle de acesso por perfil
- Isolamento das responsabilidades de identidade

📌 **Observação:**  
Este serviço **não é responsável** por compras, pedidos ou produtos.  
Ele atua exclusivamente como **provedor de identidade e segurança** para os demais microsserviços.

---

## 🌐 Porta de Execução

| Ambiente | Porta |
|-------|------|
| Desenvolvimento | 8083 |

---

## 🛠️ Tecnologias Utilizadas

- Java 21
- Spring Boot
- Maven
- Spring Data JPA
- Spring Security
- JWT (JSON Web Token)
- Banco de Dados: MySQL / MariaDB
- Docker (planejado)

---

## ⚙️ Configurações

As configurações sensíveis são feitas via **variáveis de ambiente**, garantindo segurança e compatibilidade com ambientes Docker e AWS.

### Exemplo de variáveis necessárias

```env
DB_HOST=localhost
DB_PORT=3306
DB_NAME=usuarios_db
DB_USER=usuarios_user
DB_PASSWORD=senha_segura

JWT_SECRET=chave_secreta
JWT_EXPIRATION=3600000
