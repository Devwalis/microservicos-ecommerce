# produtos-service

Microsserviço responsável pela **gestão de produtos, estoque, preços e promoções** do sistema.  
Este serviço centraliza todas as regras relacionadas ao **catálogo de produtos**, garantindo organização, escalabilidade e separação de responsabilidades dentro de uma **arquitetura de microsserviços**.

O projeto faz parte de um ambiente de estudos com foco em **boas práticas de back-end, cloud AWS, Docker e DevOps**, simulando cenários reais de produção.

---

## 📌 Responsabilidades

Este serviço é responsável por:

### 📦 Gestão de Produtos
- Cadastro de produtos
- Atualização de dados do produto
- Ativação e desativação de produtos
- Consulta de produtos por ID

### 🔍 Listagem e Busca
- Listagem completa de produtos
- Busca por nome, categoria ou status
- Paginação e ordenação (planejado)

### 📊 Controle de Estoque
- Controle de quantidade em estoque
- Atualização de estoque após compras
- Validação de disponibilidade de produtos

### 💰 Preços, Promoções e Cupons
- Atualização de preços
- Aplicação de promoções
- Gestão de cupons de desconto
- Validação de cupons para compras

📌 **Observação:**  
Este serviço **não é responsável** por usuários, autenticação, compras ou pedidos.  
Ele fornece dados e validações para outros microsserviços, como o `compras-service`.

---

## 🌐 Porta de Execução

| Ambiente | Porta |
|-------|------|
| Desenvolvimento | 8082 |

---

## 🛠️ Tecnologias Utilizadas

- Java 21
- Spring Boot
- Maven
- Spring Data JPA
- Banco de Dados: MySQL / MariaDB
- Docker (planejado)

---

## ⚙️ Configurações

As configurações sensíveis são feitas via **variáveis de ambiente**, seguindo boas práticas de segurança e compatibilidade com Docker e AWS.

### Exemplo de variáveis necessárias

```env
DB_HOST=localhost
DB_PORT=3306
DB_NAME=produtos_db
DB_USER=produtos_user
DB_PASSWORD=senha_segura
