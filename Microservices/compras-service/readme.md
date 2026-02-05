# compras-service

Microsserviço responsável pelo **fluxo de compra (pré-pedido)** do sistema.  
Este serviço representa o **ato de comprar**, funcionando como um **carrinho de compras**, onde são realizadas validações antes da criação do pedido final.

Ele atua como intermediário entre o usuário, os produtos e o serviço de pedidos, garantindo consistência e regras de negócio no processo de compra.

O projeto faz parte de um ambiente de estudos com foco em **boas práticas de back-end, cloud AWS, Docker e DevOps**, simulando cenários reais de produção.

---

## 📌 Responsabilidades

Este serviço é responsável por:

### 🛒 Carrinho de Compras
- Criar carrinho de compras
- Adicionar produtos ao carrinho
- Remover produtos do carrinho
- Consultar itens do carrinho

### 💰 Cálculo e Validações
- Calcular valor total da compra
- Aplicar cupons de desconto
- Validar disponibilidade de estoque (via `produtos-service`)
- Validar dados do usuário (via `usuarios-service`)

### ✅ Confirmação de Compra
- Confirmar compra após validações
- Enviar dados da compra para o `pedidos-service`
- Garantir consistência do processo de compra

📌 **Observação:**  
Este serviço **não é responsável** por persistir pedidos finais nem por gerenciar estoque diretamente.  
Ele coordena o fluxo de compra antes da geração do pedido oficial.

---

## 🌐 Porta de Execução

| Ambiente | Porta |
|-------|------|
| Desenvolvimento | 8080 |

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

As configurações sensíveis são feitas via **variáveis de ambiente**, garantindo segurança e compatibilidade com Docker e AWS.

### Exemplo de variáveis necessárias

```env
DB_HOST=localhost
DB_PORT=3306
DB_NAME=compras_db
DB_USER=compras_user
DB_PASSWORD=senha_segura
