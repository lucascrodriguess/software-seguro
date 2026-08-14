## 19. Monitoramento e Detecção de Intrusões

### 19.1. Conceito de Detecção de Intrusões
A **Detecção de Intrusões** (*Intrusion Detection*) é o conjunto de processos, mecanismos e ferramentas responsáveis por monitorar continuamente os eventos que ocorrem no ambiente computacional do **Comer-Tchê!** (aplicativo móvel, web, API Gateway, microsserviços, banco de dados relacional e serviços de cache como o Redis) e analisá-los em busca de indícios de atividades maliciosas, acessos não autorizados, violações de políticas de segurança ou vulnerabilidades sendo exploradas em tempo real.

Em uma arquitetura de defesa em profundidade (*Defense in Depth*), a detecção atua como a camada de visibilidade operacional, permitindo que a equipe de segurança do **Comer-Tchê!** identifique anomalias e comportamentos suspeitos que conseguiram ultrapassar as barreiras de proteção iniciais, como o WAF e o API Gateway.

---

### 19.2. Diferença entre Prevenir e Detectar

A segurança da informação da plataforma **Comer-Tchê!** baseia-se na atuação conjunta de controles preventivos e detectivos:

*   **Prevenção (*Preventative Controls*):**
    *   **Foco:** Impedir proativamente que um ataque ou falha de segurança aconteça antes que atinja os ativos críticos (como PII de clientes e dados financeiros).
    *   **Exemplos:** Validação rigorosa e recálculo do valor de pedidos no *server-side* (RS04), uso obrigatório de HTTPS/TLS, regras no WAF bloqueando SQL Injection, controle de acesso baseado em papéis (*RBAC*) e propriedade (*Ownership Check*) via *middlewares*, e criptografia de senhas com *hash* salgado.
    *   **Limitação:** Nenhuma prevenção é infalível. Falhas de dia zero (*zero-day*), erros de configuração em integrações com gateways de pagamento/mapas ou abuso de lógica de negócios (como fraudes de entregadores) podem contornar mecanismos preventivos.

*   **Detecção (*Detective Controls*):**
    *   **Foco:** Identificar, registrar e alertar quando uma tentativa de invasão está ocorrendo ou quando uma violação já se consumou no sistema.
    *   **Exemplos:** Monitoramento centralizado da trilha de auditoria (conforme o controle do R06), análise de padrões de tráfego na API, alertas de múltiplas falhas de autenticação e acompanhamento de exceções em sistemas de SIEM (*Security Information and Event Management*).
    *   **Importância:** Reduz o *dwell time* (tempo que um atacante permanece despercebido na infraestrutura do Comer-Tchê!) e viabiliza uma resposta rápida para conter danos operacionais ou vazamentos de dados pessoais.

---

### 19.3. Eventos do Sistema a Serem Registrados

Para que o sistema de detecção seja eficaz, a API, os aplicativos e a infraestrutura do **Comer-Tchê!** devem gerar registros de auditoria (*logs*) estruturados, consistentes e protegidos contra alteração (conforme detalhado no tratamento do risco **R06**). Os eventos essenciais que devem ser registrados incluem:

1.  **Eventos de Autenticação e Sessão:**
    *   Tentativas de login de Clientes, Entregadores, Lojistas e Administradores (bem-sucedidas e mal-sucedidas), contendo IP de origem, *User-Agent*, dispositivo e identificador do usuário.
    *   Solicitações de redefinição de senha, alteração de e-mail/telefone e descarte/geração de desafios MFA/OTP.
    *   Criação, renovação, revogação e invalidação de *tokens* JWT de sessão.

2.  **Controle de Acesso e Autorização:**
    *   Negações de acesso indicadas por respostas HTTP `401 Unauthorized` e HTTP `403 Forbidden` (ex.: tentativa de acesso a rotas `/api/v1/admin/*` por um cliente comum, ou tentativa de leitura de pedidos de terceiros - IDOR).
    *   Tentativas de acesso a rotas administrativas ou recursos restritos por parte de usuários não privilegiados.
    *   Alterações de permissões, papéis (*roles*) ou qualquer elevação de privilégios de contas na plataforma.

3.  **Validação de Entrada e Exceções da Aplicação:**
    *   Erros de validação de dados de entrada HTTP `400 Bad Request` (ex.: envio de corpo de requisição com formato ou tipo de dado divergente).
    *   Exceções não tratadas do servidor HTTP `500 Internal Server Error`, especialmente aquelas associadas a erros de comunicação com o banco de dados relacional ou serviços externos (Gateway de Pagamento e API de Mapas).
    *   Parâmetros de requisição contendo caracteres ou estruturas tipicamente associadas a *payloads* de ataque (ex.: `' OR '1'='1`, `<script>`, `../`) ou requisições de checkout com divergência entre o valor calculado pelo cliente e o valor do banco de dados (R03).

4.  **Modificações Críticas de Dados e Sistema:**
    *   Operações sensíveis de leitura, alteração ou exclusão em massa de dados de PII (clientes e entregadores) e registros financeiros.
    *   Ações administrativas críticas, tais como banimento/liberação de contas, conciliação manual de pagamentos e alteração de regras de cupons/promoções.
    *   Alterações em configurações globais do **Comer-Tchê!** e atualizações de infraestrutura/microsserviços.

---

### 19.4. Regras Simples de Detecção

Abaixo estão apresentadas três regras formais de detecção aplicáveis ao contexto da plataforma **Comer-Tchê!**, derivadas diretamente dos riscos e requisitos mapeados nas etapas anteriores:

| Campo | Regra 1: Ataque de Força Bruta / Credential Stuffing (R01 / RS03) | Regra 2: Tentativa de Injeção ou Adulteração de Preço (R03 / RS04) | Regra 3: Escalação de Privilégios / Acesso Não Autorizado e IDOR (R07 / R10 / RS01 / RS02) |
| :--- | :--- | :--- | :--- |
| **Risco observado** | Uso indevido de conta, autenticação abusiva e testes em massa de senhas vazadas em contas de clientes/entregadores. | Injeção de código ou tentativa de adulteração de parâmetros de pedidos/pagamentos no backend. | Acesso indevido a dados de terceiros (IDOR/BOLA) ou tentativa de acesso a rotas do painel administrativo por contas não autorizadas. |
| **Fonte de dados** | Logs de autenticação do servidor backend e do serviço de autenticação (`/api/v1/auth/login`). | Logs de requisições HTTP da API de Pedidos (`/api/v1/orders/checkout`), logs do Gateway de Pagamento e exceções do banco de dados. | Logs de acesso do *Middleware* de Autorização (*Guard*), API Gateway e logs de auditoria (Respostas HTTP `401`/`403`). |
| **Condição de alerta** | Mais de 5 tentativas de login malsucedidas sequenciais para a mesma conta ou vindas do mesmo IP/dispositivo em um intervalo de 60 segundos. | Detecção de divergência entre o valor submetido no payload do app e o total recalculado pelo servidor, ou presença de caracteres de injeção em parâmetros de requisição (`' OR '1'='1`, `UNION SELECT`). | Registro de 3 ou mais respostas HTTP `403 Forbidden` ou `401 Unauthorized` originadas da mesma sessão ou IP em até 5 minutos ao tentar acessar `/api/v1/admin/*` ou IDs de recursos de outros usuários (`/api/v1/orders/{uuid}`). |
| **Resposta inicial** | Aplicar limitação de taxa (*rate limiting*) no IP por 15 minutos, disparar fluxo de validação obrigatória por MFA/OTP para o usuário afetado e notificar o SOC (*Security Operations Center*, a equipe responsável por acompanhar os alertas de segurança) com severidade Média. | Bloquear imediatamente o IP no WAF/API Gateway por 1 hora, rejeitar o pedido, descartar o payload e gerar alerta de Alta severidade no painel de segurança. | Invalidar imediatamente os *tokens* JWT de sessão ativos do usuário, solicitar nova autenticação com revalidação de perfil e emitir alerta de Alta severidade para auditoria de acesso. |

---

### 19.5. Fluxo de Ações Pós-Alerta (Resposta a Incidentes)

Após a emissão de um alerta por qualquer uma das regras de detecção, a equipe de segurança e operações do **Comer-Tchê!** deve seguir um fluxo estruturado de resposta a incidentes composto pelas seguintes fases:

```
+------------------+     +------------------+     +------------------+     +------------------+
| 1. Triagem e     | --> | 2. Contenção     | --> | 3. Erradicação   | --> | 4. Recuperação   |
|    Validação     |     |    Imediata      |     |    e Correção    |     |    e Pós-Análise |
+------------------+     +------------------+     +------------------+     +------------------+
```

1.  **Triagem e Validação (*Triage*):**
    *   **Análise do Alerta:** Verificar os detalhes do evento emitido pela API ou WAF (IP, ID do usuário, tipo de perfil, carimbo de data/hora, rota/URI e *payload* suspeito).
    *   **Descarte de Falsos Positivos:** Confirmar se o evento reflete uma atividade maliciosa real ou uma anomalia legítima (ex.: instabilidade temporária no Gateway de Pagamentos parceiro, erro de digitação recorrente do cliente).
    *   **Classificação de Severidade:** Categorizar o incidente (Baixo, Médio, Alto, Crítico) segundo a matriz de riscos do **Comer-Tchê!** para determinar o SLA de resposta.

2.  **Contenção Imediata (*Containment*):**
    *   **Medidas de Curto Prazo:** Bloquear o IP atacante na borda (WAF/API Gateway), revogar os *tokens* JWT de sessão ativos da conta suspeita e isolar temporariamente a conta de cliente, entregador ou restaurante afetada.
    *   **Medidas de Longo Prazo:** Caso uma vulnerabilidade crítica esteja sendo explorada ativamente (ex.: falha no cálculo do checkout) sem correção imediata no código, aplicar regra temporária de bloqueio no WAF para o *endpoint* afetado ou ativar o modo de degradação controlada da API.

3.  **Erradicação e Correção (*Eradication*):**
    *   Analisar os logs de auditoria imutáveis para determinar a extensão das ações do atacante e verificar se houve vazamento de PII de clientes ou prejuízo financeiro.
    *   Identificar a causa raiz no código-fonte do backend, na regra do *middleware* ou na infraestrutura da plataforma.
    *   Desenvolver, testar e aplicar a correção de código (*patch*) ou ajuste de configuração no ambiente de produção.

4.  **Recuperação e Pós-Análise (*Recovery & Post-Mortem*):**
    *   Restabelecer o funcionamento normal dos serviços e acessos afetados no **Comer-Tchê!**.
    *   Manter o ambiente sob monitoramento intensivo para garantir que a ameaça foi totalmente neutralizada.
    *   **Relatório Pós-Incidente (*Post-Mortem*):** Documentar o evento, avaliar se os dados pessoais foram expostos (notificando o Encarregado/DPO e a ANPD, se necessário), avaliar a eficácia dos alertas e atualizar as regras de detecção para prever futuras variações de ataques.
