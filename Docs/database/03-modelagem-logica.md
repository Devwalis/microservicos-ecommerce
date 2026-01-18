# 03 — Modelagem Lógica do Banco de Dados

Este documento descreve a **modelagem lógica** do banco de dados do e-commerce, com foco em tabelas, campos, chaves primárias (PK), chaves estrangeiras (FK), normalização e regras estruturais. A modelagem está alinhada ao domínio definido anteriormente e preparada para um e-commerce pequeno, com possibilidade de evolução.

---

## 🎯 Objetivos da Modelagem Lógica

* Traduzir o domínio do negócio em estruturas relacionais
* Garantir **consistência, integridade e escalabilidade**
* Evitar redundância (3ª Forma Normal)
* Facilitar manutenção e futuras integrações

---

## 🧩 Visão Geral das Entidades

* Usuários (Clientes)
* Administradores
* Endereços
* Produtos
* Categorias
* Variações de Produto (SKU)
* Estoque
* Pedidos
* Itens do Pedido
* Pagamentos
* Tentativas de Pagamento
* Histórico de Status do Pedido
* Cupons

---

## 👤 Clientes

### Tabela: `clientes`

| Campo      | Tipo Lógico   | Observação          |
| ---------- | ------------- | ------------------- |
| id_cliente | PK            | Identificador único |
| nome       | texto         | Nome completo       |
| email      | texto (único) | Usado como login    |
| senha_hash | texto         | Senha criptografada |
| ativo      | boolean       | Controle de acesso  |
| criado_em  | datetime      | Auditoria           |

📌 Observação: clientes **não são administradores**.

---

## 👨‍💼 Administradores

### Tabela: `administradores`

| Campo      | Tipo Lógico   | Observação            |
| ---------- | ------------- | --------------------- |
| id_admin   | PK            | Identificador único   |
| nome       | texto         | Nome do administrador |
| email      | texto (único) | Login                 |
| senha_hash | texto         | Senha criptografada   |
| ativo      | boolean       | Controle              |
| criado_em  | datetime      | Auditoria             |

---

## 🏠 Endereços

### Tabela: `enderecos`

| Campo       | Tipo Lógico   | Observação       |
| ----------- | ------------- | ---------------- |
| id_endereco | PK            |                  |
| id_cliente  | FK → clientes | Proprietário     |
| apelido     | texto         | Ex: "Minha casa" |
| logradouro  | texto         |                  |
| numero      | texto         |                  |
| complemento | texto         | Opcional         |
| bairro      | texto         |                  |
| cidade      | texto         |                  |
| estado      | texto         |                  |
| cep         | texto         |                  |
| criado_em   | datetime      |                  |

📌 Um cliente pode possuir **múltiplos endereços**.

---

## 🗂️ Categorias

### Tabela: `categorias`

| Campo            | Tipo Lógico     | Observação   |
| ---------------- | --------------- | ------------ |
| id_categoria     | PK              |              |
| nome             | texto           |              |
| id_categoria_pai | FK → categorias | Subcategoria |
| ativo            | boolean         |              |

📌 Permite hierarquia (ex: Roupas → Camisas → Masculinas).

---

## 📦 Produtos

### Tabela: `produtos`

| Campo        | Tipo Lógico     | Observação      |
| ------------ | --------------- | --------------- |
| id_produto   | PK              |                 |
| id_categoria | FK → categorias |                 |
| nome         | texto           |                 |
| descricao    | texto           |                 |
| ativo        | boolean         | Disponibilidade |
| criado_em    | datetime        |                 |

---

## 🧷 Variações de Produto (SKU)

### Tabela: `produto_variacoes`

| Campo       | Tipo Lógico   | Observação          |
| ----------- | ------------- | ------------------- |
| id_variacao | PK            |                     |
| id_produto  | FK → produtos |                     |
| sku         | texto (único) | Controle de estoque |
| tamanho     | texto         | Ex: P, M, G         |
| cor         | texto         |                     |
| preco       | decimal       | Preço da variação   |
| ativo       | boolean       |                     |

📌 **Cada SKU representa uma variação única do produto**.

---

## 📊 Estoque

### Tabela: `estoque`

| Campo         | Tipo Lógico            | Observação |
| ------------- | ---------------------- | ---------- |
| id_estoque    | PK                     |            |
| id_variacao   | FK → produto_variacoes |            |
| quantidade    | inteiro                |            |
| atualizado_em | datetime               |            |

📌 Controle separado para facilitar auditoria e evolução futura.

---

## 🛒 Pedidos

### Tabela: `pedidos`

| Campo       | Tipo Lógico    | Observação          |
| ----------- | -------------- | ------------------- |
| id_pedido   | PK             |                     |
| id_cliente  | FK → clientes  |                     |
| id_endereco | FK → enderecos | Endereço usado      |
| status      | texto          | Ex: CRIADO, ENVIADO |
| valor_total | decimal        |                     |
| criado_em   | datetime       |                     |

---

## 🧾 Itens do Pedido

### Tabela: `pedido_itens`

| Campo          | Tipo Lógico            | Observação |
| -------------- | ---------------------- | ---------- |
| id_item        | PK                     |            |
| id_pedido      | FK → pedidos           |            |
| id_variacao    | FK → produto_variacoes |            |
| quantidade     | inteiro                |            |
| preco_unitario | decimal                | Snapshot   |

📌 Preço salvo para **histórico**, não depende do produto atual.

---

## 💳 Pagamentos

### Tabela: `pagamentos`

| Campo        | Tipo Lógico  | Observação               |
| ------------ | ------------ | ------------------------ |
| id_pagamento | PK           |                          |
| id_pedido    | FK → pedidos |                          |
| metodo       | texto        | PIX / CARTAO             |
| status       | texto        | Ex: PENDENTE, CONFIRMADO |
| criado_em    | datetime     |                          |

---

## 🔁 Tentativas de Pagamento

### Tabela: `pagamento_tentativas`

| Campo           | Tipo Lógico     | Observação |
| --------------- | --------------- | ---------- |
| id_tentativa    | PK              |            |
| id_pagamento    | FK → pagamentos |            |
| status          | texto           |            |
| retorno_gateway | texto           | Log        |
| criado_em       | datetime        |            |

📌 Permite múltiplas tentativas por pedido.

---

## 🧾 Histórico de Status do Pedido

### Tabela: `historico_status_pedido`

| Campo          | Tipo Lógico  | Observação      |
| -------------- | ------------ | --------------- |
| id_historico   | PK           |                 |
| id_pedido      | FK → pedidos |                 |
| status         | texto        |                 |
| alterado_por   | texto        | CLIENTE / ADMIN |
| id_responsavel | inteiro      |                 |
| criado_em      | datetime     |                 |

📌 Auditoria completa do ciclo do pedido.

---

## 🎟️ Cupons

### Tabela: `cupons`

| Campo      | Tipo Lógico   | Observação         |
| ---------- | ------------- | ------------------ |
| id_cupom   | PK            |                    |
| codigo     | texto (único) |                    |
| tipo       | texto         | FRETE / DESCONTO   |
| valor      | decimal       | Percentual ou fixo |
| valido_ate | datetime      |                    |
| ativo      | boolean       |                    |

---

## 🧠 Normalização

* **1FN**: campos atômicos
* **2FN**: dependência total da PK
* **3FN**: sem dependências transitivas

📌 Dados derivados (ex: valor total) são calculados ou armazenados por histórico.

---

## ✅ Conclusão

Esta modelagem lógica estabelece uma base **sólida, organizada e profissional**, pronta para:

* Criação do MER
* Modelagem física (MySQL)
* Separação por microserviços
* Evolução sem refatorações drásticas


