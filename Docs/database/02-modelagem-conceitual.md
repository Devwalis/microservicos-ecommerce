# Modelagem Conceitual do Banco de Dados

## 1. Objetivo

Este documento descreve a modelagem conceitual do banco de dados do sistema de e-commerce, identificando as principais entidades do domínio, seus relacionamentos e regras de negócio associadas, sem considerar detalhes técnicos de implementação.

A modelagem conceitual representa o entendimento do negócio e serve como base para a modelagem lógica e física.

---

## 2. Visão Geral do Domínio

O sistema de e-commerce é composto por um conjunto de entidades que representam:

- Usuários (clientes e administrador)
- Produtos e suas variações
- Categorias e subcategorias
- Pedidos e itens de pedido
- Pagamentos (via integração externa)
- Endereços
- Entregas
- Cupons de desconto
- Histórico de status do pedido

---

## 3. Principais Entidades do Domínio

### 3.1 Usuário

Representa qualquer pessoa que utiliza o sistema.

**Tipos de usuário:**
- Cliente
- Administrador (único)

**Responsabilidades:**
- Autenticação no sistema
- Associação com pedidos
- Associação com endereços
- Uso de cupons de desconto

---

### 3.2 Endereço

Representa locais físicos cadastrados pelo cliente para entrega.

**Características:**
- Um usuário pode possuir múltiplos endereços
- Cada endereço possui um nome identificador (ex: “Minha casa”, “Casa da minha mãe”)
- Um pedido utiliza um único endereço no momento da compra

---

### 3.3 Categoria

Representa a organização dos produtos no catálogo.

**Características:**
- Pode possuir subcategorias
- Estrutura hierárquica (categoria pai → categoria filha)
- Exemplo: Roupas → Camisas → Masculinas

---

### 3.4 Produto

Representa um item disponível para venda.

**Características:**
- Associado a uma categoria
- Possui preço base
- Possui controle de estoque
- Pode estar disponível ou indisponível

---

### 3.5 Variação de Produto

Representa variações de um produto, como tamanho ou cor.

**Exemplo:**
- Camisa tamanho P, M, G, PP

**Características:**
- Cada variação possui estoque próprio
- Associada a um único produto

---

### 3.6 Pedido

Representa uma compra realizada por um cliente.

**Características:**
- Um pedido pertence a um único usuário
- Possui múltiplos itens
- Possui status (ex: criado, pago, enviado, entregue, cancelado)
- Possui regras de cancelamento

**Regra de cancelamento:**
- O cliente pode cancelar o pedido até 24 horas após a criação
- Após esse prazo, o cancelamento depende do administrador

---

### 3.7 Item de Pedido

Representa um produto específico dentro de um pedido.

**Características:**
- Relaciona pedido + variação de produto
- Armazena quantidade e preço no momento da compra
- Garante histórico imutável do pedido

---

### 3.8 Pagamento

Representa o processo de pagamento do pedido.

**Características:**
- Integração com API externa (Pix e Cartão de Crédito)
- O sistema não implementa motor de pagamento próprio
- Em ambientes de teste, pode operar em modo de simulação

---

### 3.9 Entrega

Representa o envio físico do pedido ao cliente.

**Características:**
- Associada a um pedido
- Utiliza Correios
- Pode conter código de rastreamento
- Controlada pelo administrador

---

### 3.10 Cupom de Desconto

Representa benefícios promocionais concedidos aos clientes.

**Tipos de cupom:**
- Frete grátis para região Nordeste
- Descontos de 30% a 50% para outras regiões

**Regras:**
- Cupom gerado por indicação de novos clientes
- Pode ter restrições geográficas
- Possui validade e regras de uso

---

### 3.11 Histórico de Status do Pedido

Representa o inventário de eventos que ocorrem durante o ciclo de vida do pedido.

**Exemplos de status:**
- Pedido criado
- Pagamento confirmado
- Pedido enviado
- Pedido entregue
- Pedido cancelado

**Objetivo:**
- Garantir rastreabilidade
- Transparência para o cliente
- Base para auditoria futura

---

## 4. Relacionamentos (Visão Conceitual)

- Usuário → Endereço (1:N)
- Usuário → Pedido (1:N)
- Pedido → Item de Pedido (1:N)
- Item de Pedido → Variação de Produto (N:1)
- Produto → Variação de Produto (1:N)
- Categoria → Produto (1:N)
- Categoria → Categoria (auto-relacionamento)
- Pedido → Pagamento (1:1)
- Pedido → Entrega (1:1)
- Pedido → Histórico de Status (1:N)
- Usuário → Cupom (1:N)

---

## 5. Considerações Importantes

- O banco de dados não armazena dados sensíveis de cartão
- O preço do item é congelado no momento da compra
- O histórico do pedido não deve ser sobrescrito
- Integrações externas devem ser desacopladas do domínio central

---

## 6. Próximo Passo

O próximo documento irá traduzir esta modelagem conceitual para uma **modelagem lógica**, definindo:

- Entidades em formato relacional
- Atributos principais
- Chaves primárias e estrangeiras
- Normalização inicial
