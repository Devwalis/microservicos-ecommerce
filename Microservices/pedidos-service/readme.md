pedidos-service

Microsserviço responsável pela **gestão de pedidos finalizados e histórico de compras** do sistema.  
Este serviço recebe uma **compra confirmada** e a transforma em um **pedido oficial**, garantindo rastreabilidade, consistência e histórico para os usuários.

O projeto faz parte de um ambiente de estudos com foco em **boas práticas de back-end, cloud AWS, Docker e DevOps**, simulando cenários reais de produção.

---

## 📌 Responsabilidades

Este serviço é responsável por:

### 📄 Gestão de Pedidos
- Criar pedidos a partir de compras confirmadas
- Persistir o histórico de pedidos
- Consultar pedidos por usuário
- Consultar pedidos por ID

### 🔄 Status e Acompanhamento
- Atualizar status do pedido (ex.: CRIADO, PAGO, ENVIADO, CANCELADO)
- Controlar o ciclo de vida do pedido

### 📊 Relatórios
- Gerar relatórios simples de pedidos
- Consultas para análises básicas (quantidade, status, período)

📌 **Observação:**  
Este serviço **não é responsável** por autenticação, produtos ou carrinho de compras.  
Ele consome informações de outros serviços para manter o pedido consistente.

---

## 🌐 Porta de Execução

| Ambiente | Porta |
|-------|------|
| Desenvolvimento | 8081 |

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
DB_NAME=pedidos_db
DB_USER=pedidos_user
DB_PASSWORD=senha_segura
