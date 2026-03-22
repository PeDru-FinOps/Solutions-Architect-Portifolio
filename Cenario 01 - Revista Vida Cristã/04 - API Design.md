# Especificação de API – MVP (Monólito Modular)

## Status
Proposto

---

# 1. Visão Geral

Este documento define os contratos de API para o sistema MVP responsável por:

- Gerenciamento de pedidos
- Processamento de pagamentos via PIX
- Rastreabilidade financeira
- Integração com serviços externos (Google Auth e PSP)

O sistema segue uma Arquitetura de Monólito Modular, onde as APIs representam a fronteira de interação entre atores externos e os módulos internos.

---

# 2. Princípios Arquiteturais

- Design RESTful
- Comunicação baseada em JSON
- Autenticação stateless (JWT)
- Autenticação externalizada (Google OAuth)
- Processamento de pagamentos externalizado (PSP)
- Idempotência no processamento de pagamentos
- Consistência forte para operações financeiras

---

# 3. Integrações Externas

## 3.1 Autenticação Google

- Utilizada para autenticação do usuário
- Backend valida o token recebido
- Sistema gera um JWT interno

## 3.2 Provedor de Pagamento (PSP)

- Responsável pela geração e processamento do PIX
- Envia confirmação de pagamento via webhook

---

# 4. Modelo de Autenticação

## 4.1 Fluxo

1. Usuário autentica via Google no frontend
2. Frontend envia o token Google para o backend
3. Backend valida o token
4. Backend cria ou recupera o usuário
5. Backend retorna um JWT interno

## 4.2 Uso do Token

Todos os endpoints protegidos requerem:

Authorization: Bearer <jwt>

---

# 5. Endpoints da API

## 5.1 Módulo Auth

### POST /auth/google

Descrição:
Autentica o usuário utilizando token do Google.

Request:
{
  "google_token": "string"
}

Response:
{
  "access_token": "jwt",
  "user": {
    "id": "uuid",
    "nome": "string",
    "email": "string"
  }
}

---

## 5.2 Módulo Users

### GET /users/me

Descrição:
Retorna os dados do usuário autenticado.

Response:
{
  "id": "uuid",
  "nome": "string",
  "email": "string",
  "tipo": "ADMIN | SUPERVISORA | MEMBRO"
}

---

## 5.3 Módulo Catálogo

### GET /revistas

Descrição:
Lista as revistas disponíveis para venda.

Response:
[
  {
    "id": "uuid",
    "edicao": "string",
    "preco": 0.00,
    "img": "string"
  }
]

---

## 5.4 Módulo Pedidos

### POST /pedidos

Descrição:
Cria um novo pedido.

Request:
{
  "revista_id": "uuid",
  "quantidade": 1
}

Regras de Processamento:

- Usuário é obtido via JWT
- Associação e região são resolvidas internamente
- Valor total é calculado no backend
- Status inicial do pedido: PENDENTE

Response:
{
  "id": "uuid",
  "status": "PENDENTE",
  "valor_total": 0.00
}

---

### GET /pedidos

Descrição:
Lista os pedidos do usuário autenticado.

---

### GET /pedidos/{id}

Descrição:
Retorna os detalhes de um pedido.

---

## 5.5 Módulo Pagamentos

### POST /pagamentos/{pedido_id}/pix

Descrição:
Gera cobrança PIX para um pedido.

Regras de Processamento:

- Validar existência do pedido
- Garantir que o pedido não esteja pago
- Criar pagamento (status: GERADO)
- Chamar PSP para geração do PIX
- Armazenar txid_pix

Response:
{
  "qr_code": "string",
  "copia_e_cola": "string",
  "txid": "string",
  "expires_at": "datetime"
}

---

### GET /pagamentos/{pedido_id}

Descrição:
Consulta o pagamento associado ao pedido.

---

## 5.6 Webhook (Integração PSP)

### POST /webhook/pix

Descrição:
Recebe confirmação de pagamento enviada pelo PSP.

Request:
{
  "txid": "string",
  "status": "CONFIRMED"
}

Regras de Processamento:

1. Buscar pagamento pelo txid
2. Garantir idempotência:
   - Se já estiver confirmado → ignorar
3. Atualizar pagamento → CONFIRMADO
4. Atualizar pedido → PAGO
5. Executar em transação atômica

Response:
{
  "status": "ok"
}

---

## 5.7 Módulo Administração (MVP)

### GET /admin/pedidos

Descrição:
Lista todos os pedidos.

### GET /admin/pagamentos

Descrição:
Lista todos os pagamentos.

---

# 6. Estados do Sistema

## Pedido

- PENDENTE
- PAGO
- CANCELADO

## Pagamento

- GERADO
- CONFIRMADO

---

# 7. Regras de Negócio

## Integridade Financeira

- Todo pagamento deve estar vinculado a um pedido
- Um pedido só pode ser marcado como pago após confirmação do pagamento

## Idempotência

- O processamento do webhook deve ser idempotente
- Eventos duplicados não devem causar efeitos colaterais

## Consistência

- Atualização de pagamento e pedido deve ocorrer na mesma transação

## Segurança

- Validar origem do webhook (PSP)
- Não confiar em dados do frontend
- Sempre calcular valores financeiros no backend

---

# 8. Tratamento de Erros

- 400 → Requisição inválida
- 401 → Não autenticado
- 404 → Recurso não encontrado
- 409 → Conflito
- 500 → Erro interno

---

# 9. Fora do Escopo (MVP)

- Pagamentos parciais
- Múltiplos métodos de pagamento
- Reembolsos
- Relatórios avançados

---

# 10. Evoluções Futuras

- Expiração automática de pagamentos
- Arquitetura orientada a eventos
- Analytics e relatórios
- Multi-tenant

---

# 11. Conclusão

Esta API garante:

- Rastreabilidade ponta a ponta
- Consistência financeira
- Evolução modular
- Integração com serviços externos

O sistema está pronto para implementação com baixo risco arquitetural.