# 04 – Modelagem Física do Banco de Dados

## 🎯 Objetivo

Este documento descreve a modelagem física do banco de dados do e-commerce, considerando:

- Arquitetura de microserviços
- Uso de MariaDB 11.8.x (compatível com MySQL 8.x)
- Persistência por serviço (Database per Service)
- Boas práticas de performance, segurança e escalabilidade

O foco é como os dados são armazenados fisicamente, servindo de base direta para:

- Scripts SQL
- Diagramas ER
- Decisões de infraestrutura

## 🧱 Estratégia de Banco por Microserviço

Adotamos o padrão **Database per Service**.

Cada microserviço possui:
- Seu próprio banco
- Seu usuário exclusivo
- Isolamento total de dados

## 📦 Serviços e Bancos

| Microserviço | Banco de Dados |
|--------------|----------------|
| usuarios-service | usuarios_db |
| produtos-service | produtos_db |
| pedidos-service | pedidos_db |
| compras-service | compras_db |

## ✅ Vantagens

- Isolamento de falhas
- Evolução independente de schema
- Maior segurança
- Alinhado com DDD e microserviços

## 🗄️ Padrões Gerais de Modelagem Física

### 🔢 Identificadores (Primary Key)

| Padrão | Valor |
|--------|-------|
| Tipo | BIGINT |
| Estratégia | AUTO_INCREMENT |
| Motivo | Performance e simplicidade |

```sql
id BIGINT NOT NULL AUTO_INCREMENT PRIMARY KEY


DEFAULT CHARSET=utf8mb4
COLLATE=utf8mb4_unicode_ci


## 👤 usuarios_db (usuarios-service)

### 📄 Tabela: usuarios

| Campo | Tipo | Observação |
|-------|------|------------|
| id | BIGINT PK | Identificador |
| nome | VARCHAR(255) | Nome do usuário |
| email | VARCHAR(255) | Único |
| senha_hash | VARCHAR(255) | Hash seguro (bcrypt) |
| tipo | ENUM('CLIENTE', 'ADMIN') | Tipo de usuário |
| ativo | BOOLEAN | Soft delete (1=ativo, 0=inativo) |
| created_at | DATETIME | Criação |
| updated_at | DATETIME | Atualização |
| deleted_at | DATETIME | Exclusão lógica |

**Índices:**
- UNIQUE (email)

**📌 Observação:** Senhas nunca são armazenadas em texto puro.

### 📄 Tabela: enderecos

| Campo | Tipo | Observação |
|-------|------|------------|
| id | BIGINT PK | Identificador |
| usuario_id | BIGINT | FK lógica para usuarios.id |
| apelido | VARCHAR(50) | Ex: Casa, Trabalho |
| logradouro | VARCHAR(255) | Rua / Av |
| numero | VARCHAR(20) | Número |
| complemento | VARCHAR(100) | Complemento |
| bairro | VARCHAR(100) | Bairro |
| cidade | VARCHAR(100) | Cidade |
| estado | VARCHAR(2) | UF (sigla) |
| cep | VARCHAR(9) | CEP (formato: 00000-000) |
| ativo | BOOLEAN | Soft delete (1=ativo, 0=inativo) |
| created_at | DATETIME | Data de criação |
| updated_at | DATETIME | Data de atualização |

**📌 Observação:** Um usuário pode ter vários endereços persistidos.

## 📦 produtos_db (produtos-service)

### 📄 Tabela: categorias

| Campo | Tipo | Observação |
|-------|------|------------|
| id | BIGINT PK | Identificador |
| nome | VARCHAR(100) | Nome da categoria |
| descricao | TEXT | Descrição da categoria |
| categoria_pai_id | BIGINT | FK para auto-relacionamento (hierarquia) |
| ativo | BOOLEAN | 1=ativo, 0=inativo |
| created_at | DATETIME | Data de criação |
| updated_at | DATETIME | Data de atualização |

**📌 Observação:** Permite hierarquia: Roupas → Camisas → Masculinas

### 📄 Tabela: produtos

| Campo | Tipo | Observação |
|-------|------|------------|
| id | BIGINT PK | Identificador |
| nome | VARCHAR(255) | Nome do produto |
| descricao | TEXT | Descrição detalhada |
| marca | VARCHAR(100) | Marca do produto |
| categoria_id | BIGINT | FK para categorias.id |
| ativo | BOOLEAN | 1=ativo, 0=inativo |
| created_at | DATETIME | Data de criação |
| updated_at | DATETIME | Data de atualização |
| deleted_at | DATETIME | Data de exclusão lógica |

### 📄 Tabela: produto_variacoes

Cada variação possui um SKU único.

| Campo | Tipo | Observação |
|-------|------|------------|
| id | BIGINT PK | Identificador |
| produto_id | BIGINT | FK para produtos.id |
| sku | VARCHAR(50) | Código SKU único |
| tamanho | VARCHAR(10) | P, M, G, PP, GG, etc |
| cor | VARCHAR(50) | Cor da variação |
| material | VARCHAR(100) | Material da variação |
| preco | DECIMAL(10,2) | Preço da variação |
| estoque | INT | Quantidade disponível |
| estoque_minimo | INT | Estoque mínimo para alerta |
| peso | DECIMAL(6,3) | Peso em kg |
| dimensoes | VARCHAR(50) | Dimensões (LxAxC) |
| ativo | BOOLEAN | 1=ativo, 0=inativo |
| created_at | DATETIME | Data de criação |
| updated_at | DATETIME | Data de atualização |

**Índices:**
- UNIQUE (sku)
- INDEX (produto_id)

**📌 Observação:** Estoque é controlado por variação, não pelo produto base.

### 📄 Tabela: produto_imagens

| Campo | Tipo | Observação |
|-------|------|------------|
| id | BIGINT PK | Identificador |
| produto_variacao_id | BIGINT | FK para produto_variacoes.id |
| url_imagem | VARCHAR(500) | URL da imagem |
| ordem | INT | Ordem de exibição |
| principal | BOOLEAN | Indica se é imagem principal |
| ativo | BOOLEAN | 1=ativo, 0=inativo |
| created_at | DATETIME | Data de criação |

## 🧾 pedidos_db (pedidos-service)

### 📄 Tabela: pedidos

| Campo | Tipo | Observação |
|-------|------|------------|
| id | BIGINT PK | Identificador |
| usuario_id | BIGINT | ID do cliente |
| endereco_id | BIGINT | Endereço de entrega |
| codigo_pedido | VARCHAR(20) | Código único do pedido (ex: PED-2024-001) |
| status | ENUM('AGUARDANDO_PAGAMENTO', 'PAGAMENTO_APROVADO', 'EM_PREPARACAO', 'ENVIADO', 'ENTREGUE', 'CANCELADO', 'REEMBOLSADO') | Status atual |
| valor_total | DECIMAL(10,2) | Valor final do pedido |
| valor_frete | DECIMAL(10,2) | Valor do frete |
| metodo_entrega | VARCHAR(50) | Método de entrega escolhido |
| data_entrega_estimada | DATE | Data estimada para entrega |
| data_entrega_real | DATE | Data real da entrega |
| cancelado_em | DATETIME | Data/hora do cancelamento |
| motivo_cancelamento | TEXT | Motivo do cancelamento |
| created_at | DATETIME | Data de criação |
| updated_at | DATETIME | Data de atualização |

**Índices:**
- UNIQUE (codigo_pedido)
- INDEX (usuario_id)
- INDEX (status)

**📌 Observação sobre cancelamento:** Até 24h automaticamente, após isso apenas administrador.

### 📄 Tabela: pedido_itens

| Campo | Tipo | Observação |
|-------|------|------------|
| id | BIGINT PK | Identificador |
| pedido_id | BIGINT | FK para pedidos.id |
| produto_variacao_id | BIGINT | SKU comprado |
| produto_nome | VARCHAR(255) | Nome do produto no momento da compra |
| variacao_descricao | VARCHAR(255) | Descrição da variação no momento da compra |
| quantidade | INT | Quantidade comprada |
| preco_unitario | DECIMAL(10,2) | Snapshot do preço na compra |
| subtotal | DECIMAL(10,2) | Quantidade × Preço Unitário |
| created_at | DATETIME | Data de criação |

**Índices:**
- INDEX (pedido_id)
- INDEX (produto_variacao_id)

**📌 Observação:** Não depende do preço atual do produto.

### 📄 Tabela: pedido_status_historico

| Campo | Tipo | Observação |
|-------|------|------------|
| id | BIGINT PK | Identificador |
| pedido_id | BIGINT | FK para pedidos.id |
| status_anterior | ENUM('AGUARDANDO_PAGAMENTO', 'PAGAMENTO_APROVADO', 'EM_PREPARACAO', 'ENVIADO', 'ENTREGUE', 'CANCELADO', 'REEMBOLSADO') | Status anterior |
| status_novo | ENUM('AGUARDANDO_PAGAMENTO', 'PAGAMENTO_APROVADO', 'EM_PREPARACAO', 'ENVIADO', 'ENTREGUE', 'CANCELADO', 'REEMBOLSADO') | Novo status |
| alterado_por | ENUM('CLIENTE', 'ADMIN', 'SISTEMA') | Quem alterou o status |
| observacao | TEXT | Observação sobre a alteração |
| created_at | DATETIME | Data da alteração |

**Índices:**
- INDEX (pedido_id)
- INDEX (created_at)

**📌 Observação:** Auditoria completa do pedido.

## 💳 compras_db (compras-service)

### 📄 Tabela: pagamentos

| Campo | Tipo | Observação |
|-------|------|------------|
| id | BIGINT PK | Identificador |
| pedido_id | BIGINT | FK lógica para pedido |
| tipo | ENUM('PIX', 'CARTAO_CREDITO', 'CARTAO_DEBITO', 'BOLETO', 'SIMULACAO') | Tipo de pagamento |
| status | ENUM('AGUARDANDO', 'PROCESSANDO', 'APROVADO', 'RECUSADO', 'ESTORNADO', 'CANCELADO') | Status do pagamento |
| tentativa | INT | Número da tentativa (começa em 1) |
| valor | DECIMAL(10,2) | Valor do pagamento |
| metodo_cartao | VARCHAR(50) | Método do cartão (se aplicável) |
| parcelas | INT | Número de parcelas (se aplicável) |
| external_reference | VARCHAR(100) | ID do gateway externo |
| dados_pagamento | JSON | Dados completos do pagamento (criptografado) |
| data_expiracao | DATETIME | Data de expiração do pagamento |
| data_aprovacao | DATETIME | Data de aprovação |
| motivo_recusa | TEXT | Motivo da recusa (se aplicável) |
| created_at | DATETIME | Data de criação |
| updated_at | DATETIME | Data de atualização |

**Índices:**
- INDEX (pedido_id)
- INDEX (external_reference)
- INDEX (status)

**📌 Observação:** Um pedido pode ter múltiplas tentativas de pagamento.

### 📄 Tabela: transacoes_financeiras

| Campo | Tipo | Observação |
|-------|------|------------|
| id | BIGINT PK | Identificador |
| pagamento_id | BIGINT | FK para pagamentos.id |
| tipo_transacao | ENUM('PAGAMENTO', 'ESTORNO', 'REEMBOLSO') | Tipo da transação |
| valor | DECIMAL(10,2) | Valor da transação |
| status | ENUM('SUCESSO', 'FALHA', 'PENDENTE') | Status da transação |
| codigo_transacao | VARCHAR(100) | Código único da transação |
| descricao | TEXT | Descrição da transação |
| dados_transacao | JSON | Dados completos da transação |
| created_at | DATETIME | Data de criação |
| processado_em | DATETIME | Data de processamento |

**Índices:**
- UNIQUE (codigo_transacao)
- INDEX (pagamento_id)