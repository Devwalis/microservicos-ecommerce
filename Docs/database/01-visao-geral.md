# Visão Geral do Banco de Dados

## 1. Introdução

Este documento descreve a visão geral do banco de dados do sistema de e-commerce web, cujo objetivo é suportar as operações de venda online de produtos físicos, inicialmente focado em categorias como roupas e joias.

O banco de dados é responsável por armazenar, organizar e garantir a integridade das informações relacionadas a usuários, produtos, pedidos, pagamentos, entregas e controle administrativo do sistema.

---

## 2. Objetivo do Banco de Dados

O banco de dados tem como principais objetivos:

- Persistir dados de clientes e administrador
- Armazenar produtos, categorias e estoque
- Registrar pedidos e seus estados ao longo do tempo
- Controlar autenticação e autorização de usuários
- Garantir integridade, segurança e consistência das informações
- Apoiar futuras evoluções do sistema

---

## 3. Contexto do Sistema

O sistema consiste em um e-commerce web com as seguintes características:

- Um **administrador único**, responsável pela gestão completa da loja
- Clientes que realizam cadastro, autenticação e compras
- Venda de produtos físicos organizados por categorias
- Processo de entrega realizado via Correios
- Controle de pedidos desde a criação até a entrega

O banco de dados atua como o núcleo central de dados, sendo acessado por serviços de backend responsáveis pela lógica de negócio.

---

## 4. Perfis de Usuário

### 4.1 Cliente
- Realiza cadastro no sistema
- Autentica-se utilizando email e senha
- Pode realizar pedidos
- Pode consultar histórico de compras e status de pedidos

### 4.2 Administrador
- Usuário único do sistema
- Gerencia produtos, categorias e estoque
- Controla pedidos e entregas
- Atualiza status de pedidos e informações de envio

---

## 5. Autenticação e Segurança

- O sistema utiliza **email como identificador único de login**
- Senhas são armazenadas de forma **criptografada (hash)**
- Controle de acesso baseado em perfil de usuário
- O banco não armazena senhas em texto puro

---

## 6. Escopo Inicial do Banco

Inclui:
- Usuários
- Produtos
- Categorias
- Pedidos
- Itens de pedido
- Endereços
- Pagamentos
- Entregas

Não inclui inicialmente:
- Múltiplos vendedores
- Integrações com marketplaces externos
- Pagamentos internacionais

---

## 7. Considerações Futuras

O modelo de dados foi pensado para permitir futuras evoluções, como:
- Inclusão de novos meios de pagamento
- Expansão de categorias
- Relatórios analíticos
- Auditoria e histórico de alterações
