## 3. Usuários, ativos e pontos de interação

### 3.1 Usuários e Perfis de Acesso
* **Cliente:** Navega pelos restaurantes, faz pedidos, efetua pagamentos, avalia serviços e acompanha entregas em tempo real.
* **Restaurante (Lojista):** Cadastra e atualiza cardápios, aceita/rejeita pedidos, altera status de preparação e gerencia receita.
* **Entregador:** Aceita solicitações de entrega, atualiza geolocalização e confirma o recebimento do pedido no destino final.
* **Administrador do Sistema:** Possui acesso irrestrito para gerenciar usuários, conciliar pagamentos, aplicar promoções, banir contas e auditar o sistema.

### 3.2 Ativos e Informações Protegidas (Recursos Críticos)
* **Dados Pessoais dos Usuários (PII):** Nome, CPF, e-mail, telefone e endereço completo dos clientes e entregadores.
* **Credenciais de Acesso:** Senhas (hashes), tokens JWT de sessão e chaves de API.
* **Dados Financeiros e de Pagamento:** Chaves Pix, cartões de crédito tokenizados e histórico de transações financeiras.
* **Dados de Geolocalização:** Localização GPS dos entregadores e clientes transmitidos em tempo real.
* **Métricas do Negócio:** Histórico de faturamento dos restaurantes e volume global de pedidos da plataforma.
* **Infraestrutura e Bancos de Dados:** Banco de dados relacional, servidor web/APIs e serviços de cache (Redis).

### 3.3 Pontos de Interação e Serviços Externos
* **Aplicativo Móvel e Web Client:** Interfaces de navegação do Cliente, Restaurante e Entregador.
* **API Gateway / Backend:** Centralizador de requisições REST/WebSockets.
* **Gateway de Pagamento Externo:** Integração via API de terceiros (ex: Mercado Pago, Stripe, Pagar.me).
* **Serviço de Mapas/Geolocalização:** API externa de roteamento (ex: Google Maps API, Mapbox).
