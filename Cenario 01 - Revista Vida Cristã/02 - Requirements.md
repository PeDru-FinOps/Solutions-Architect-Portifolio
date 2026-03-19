## Functional Requirements

- Auth:
  - Usuário deve ser capaz de se registrar e fazer login
  - Sistema deve autenticar via token 
  - Sistema deve restringir acesso a endpoints autenticados

- Pedidos:
  - Usuário deve ser capaz de criar um pedido
  - Usuário deve visualizar seus pedidos
  - Pedido deve iniciar com status PENDENTE

- Pagamentos:
  - Sistema deve gerar cobrança PIX vinculada a um pedido
  - Sistema deve atualizar automaticamente o status do pedido após confirmação do pagamento
  - Sistema deve garantir que um pagamento não seja processado mais de uma vez (idempotência)

- Catálogo:
  - Usuário deve visualizar lista de revistas disponíveis
  - Sistema deve exibir preço e edição

- Administração:
  - Admin deve visualizar pedidos e pagamentos

## Constraints

- O sistema deve ser implementado utilizando serviços gratuitos ou de baixo custo
- Deve ser possível rodar o sistema em ambiente local sem dependência de serviços pagos

## Non-Functional Requirements

### Cost Efficiency

- O sistema deve operar utilizando infraestrutura gratuita ou de baixo custo no MVP
- Deve suportar execução local para desenvolvimento
- Deve utilizar banco de dados único (single database)

### Availability

- O sistema pode ficar indisponível durante manutenção entre 02:00 e 05:00
- Fora desse período, deve estar disponível para uso

### Scalability

- O sistema deve permitir aumento de carga sem refatoração significativa
- A arquitetura deve permitir separação futura em serviços independentes

### Security

- A autenticação deve ser obrigatória para operações protegidas
- Toda comunicação deve usar HTTPS
- Credenciais não devem estar hard-coded
- O frontend não deve acessar diretamente o banco de dados

### Reliability and Fault Tolerance

- Falhas em serviços auxiliares (ex: envio de email) não devem impactar a conclusão do pedido
- A confirmação de pagamento deve ser persistida de forma confiável

### Maintainability

- O código deve seguir arquitetura modular
- O código deve ser versionado em Git
- Deve existir documentação mínima para setup e execução

### Observability

- O sistema deve registrar logs de:
  - eventos de pagamento
  - erros
  - tentativas de autenticação

- Logs devem ser armazenados por pelo menos 30 dias

- Deve ser possível identificar falhas através dos logs

- O sistema deve emitir alertas básicos em caso de indisponibilidade

### Deployment

- Deploy deve ser automatizado via CI/CD
- Deve ser possível realizar rollback em caso de falha

### Data Integrity

- Transações de pagamento devem ser ACID
- O sistema deve garantir consistência entre pedido e pagamento
- Nenhum pagamento pode existir sem um pedido associado

### Idenpotency

- O sistema deve garantir idempotência no processamento de pagamentos
- Webhooks duplicados não devem gerar efeitos colaterais

### Rastreability

- O sistema deve permitir rastrear cada pagamento até o pedido e usuário correspondente

## SLA and SLO Considerations

Maintenance Window: the Business authorizes scheduled maintenance between 2:00AM and 5:00AM, if necessary.