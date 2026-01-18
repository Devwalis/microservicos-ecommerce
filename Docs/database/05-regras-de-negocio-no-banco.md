# 05 – Regras de Negócio no Banco de Dados

## 🎯 Objetivo

Este documento define **quais regras de negócio são implementadas diretamente no banco de dados** do e-commerce, utilizando:

* `CHECK`
* `UNIQUE`
* `FOREIGN KEY`
* Constraints estruturais

E também deixa **explicitamente claro o que NÃO deve ser implementado no banco**, ficando sob responsabilidade da aplicação.

O objetivo é:

* Garantir integridade dos dados
* Evitar estados inválidos
* Reduzir bugs estruturais
* Facilitar criação de MER / DER

---

## 🧱 Princípios Adotados

* Arquitetura de **microserviços**
* Padrão **Database per Service**
* MariaDB 11.8.x (compatível MySQL 8.x)
* Regras **imutáveis** no banco
* Regras **de fluxo** na aplicação

---

## 👤 usuarios_db (usuarios-service)

### Tabela: usuarios

#### Regras no banco

```sql
UNIQUE (email)
CHECK (email IS NOT NULL)
```

📌 Justificativa:

* Email identifica unicamente o usuário
* Evita duplicidade estrutural

📌 O que NÃO vai para o banco:

* Validação de formato de email
* Força de senha
* Permissões (RBAC)

---

### Tabela: enderecos

#### Regras no banco

```sql
CHECK (usuario_id IS NOT NULL)
```

📌 Justificativa:

* Endereço sempre pertence a um usuário

📌 O que NÃO vai para o banco:

* Limite de endereços por usuário
* Regras de entrega

---

## 📦 produtos_db (produtos-service)

### Tabela: categorias

#### Regras no banco

```sql
CHECK (nome IS NOT NULL)
```

📌 Categoria pai é opcional

---

### Tabela: produtos

#### Regras no banco

```sql
CHECK (nome IS NOT NULL)
```

---

### Tabela: produto_variacoes

#### Regras no banco

```sql
UNIQUE (sku)
CHECK (preco >= 0)
CHECK (estoque >= 0)
```

📌 Justificativa:

* SKU identifica unicamente cada variação
* Estoque e preço nunca podem ser negativos

📌 O que NÃO vai para o banco:

* Regras de promoção
* Regras de frete
* Disponibilidade regional

---

## 🧾 pedidos_db (pedidos-service)

### Tabela: pedidos

#### Status permitidos

```sql
CHECK (status IN (
  'CRIADO',
  'AGUARDANDO_PAGAMENTO',
  'PAGO',
  'EM_SEPARACAO',
  'ENVIADO',
  'ENTREGUE',
  'CANCELADO'
))
```

#### Regras adicionais

```sql
CHECK (valor_total >= 0)
```

📌 O que NÃO vai para o banco:

* Regra de cancelamento em até 24h
* Validação de quem pode cancelar

---

### Tabela: pedido_itens

#### Regras no banco

```sql
CHECK (quantidade > 0)
CHECK (preco_unitario >= 0)
```

📌 Justificativa:

* Snapshot de preço
* Quantidade mínima garantida

---

### Tabela: pedido_status_historico

#### Regras no banco

```sql
CHECK (alterado_por IN ('CLIENTE', 'ADMIN'))
```

📌 Auditoria completa de status

---

## 💳 compras_db (compras-service)

### Tabela: pagamentos

#### Tipos de pagamento

```sql
CHECK (tipo IN ('PIX', 'CARTAO', 'SIMULACAO'))
```

#### Status de pagamento

```sql
CHECK (status IN (
  'PENDENTE',
  'PROCESSANDO',
  'APROVADO',
  'RECUSADO',
  'CANCELADO'
))
```

#### Outras regras

```sql
CHECK (tentativa >= 1)
```

📌 Justificativa:

* Suporte a múltiplas tentativas
* Integração com APIs externas

📌 O que NÃO vai para o banco:

* Comunicação com gateway
* Timeouts
* Reprocessamento

---

## 🔗 Foreign Keys e Isolamento

* Não existem FKs entre bancos diferentes
* Relacionamentos entre serviços são **lógicos**, não físicos
* Cada serviço garante sua própria integridade

📌 Exemplo:

* `pedido.usuario_id` **não referencia fisicamente** `usuarios.id`

---

## 🗑️ Soft Delete

Todas as tabelas principais utilizam:

```sql
ativo BOOLEAN DEFAULT TRUE,
deleted_at DATETIME NULL
```

📌 Regras:

* FK continuam válidas
* Histórico preservado
* Exclusão lógica controlada pela aplicação

---

## 🚫 Regras que NÃO devem ir para o banco

Essas regras ficam **exclusivamente na aplicação**:

* Cancelamento por tempo (24h)
* Regras de desconto
* Regras de cupom
* Regras de frete
* Permissões e papéis
* Validação de dados complexos

📌 Motivo:

> Bancos garantem integridade, não fluxo de negócio.



