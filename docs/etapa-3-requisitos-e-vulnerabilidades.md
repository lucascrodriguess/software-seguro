## 11. Requisitos de segurança e vulnerabilidades

Nesta seção os riscos mais críticos da Etapa 2 viram **requisitos de segurança**: frases que dizem o que o sistema tem de fazer, escritas de um jeito que dá para testar depois. Em seguida, cada risco é ligado a uma vulnerabilidade já catalogada na literatura, usando os três catálogos reconhecidos que a disciplina indica:

- **CWE** (*Common Weakness Enumeration*), uma lista numerada de tipos de fraqueza de software mantida pela MITRE. Serve para dar nome técnico e universal ao problema.
- **OWASP Top 10**, o ranking das dez categorias de falha mais relevantes em aplicações web, revisado periodicamente. Usamos a edição de 2021.
- **OWASP ASVS** (*Application Security Verification Standard*), uma lista de requisitos de segurança verificáveis, organizada por assunto, útil justamente para escrever critério de teste.

### 11.1 Requisitos de segurança

Cada requisito abaixo nasce de um risco da seção 8.4 e vem com um critério de verificação. O critério é o que transforma o requisito em algo checável: ele descreve uma ação concreta e a resposta que o sistema deve dar. Os códigos citados são respostas padrão do protocolo HTTP, em que `403 Forbidden` significa "você está identificado, mas não pode fazer isso" e `404 Not Found` significa "esse recurso não existe para você".

| ID | Risco de origem | Requisito de segurança | Critério de verificação |
| :--- | :--- | :--- | :--- |
| **RS01** | **R10** (elevação de privilégio) | O servidor deve validar o perfil do usuário e suas permissões explícitas em 100% das requisições para rotas administrativas. | Qualquer requisição para `/api/v1/admin/*` feita com um token de sessão de perfil `CLIENTE` ou `ENTREGADOR` deve retornar `403 Forbidden` e registrar um evento de auditoria. |
| **RS02** | **R07** (leitura de pedidos alheios) | A API deve verificar se o recurso pedido pertence ao usuário autenticado antes de devolver dados sensíveis. Essa checagem é chamada de verificação de propriedade (*ownership check*). | Uma requisição `GET /api/v1/orders/{order_id}` do Usuário A para um pedido do Usuário B deve retornar `403 Forbidden` ou `404 Not Found`. |
| **RS03** | **R01** (uso indevido de conta) | O sistema deve exigir verificação adicional quando o login vier de um aparelho ou endereço de rede não reconhecido. Essa verificação é o segundo fator: além da senha, um código de uso único enviado por e-mail ou SMS. | Login vindo de aparelho ou origem inédita deve ficar em estado `MFA_PENDING`, bloqueando transações até o código ser validado. |
| **RS04** | **R03 / R04** (adulteração de preço e de pagamento) | O servidor deve recalcular o valor do pedido a partir do banco de dados e validar a assinatura criptográfica das confirmações enviadas pelo gateway de pagamento. | Enviar no checkout um corpo de requisição com `"total_amount": 0.01` para um pedido de R$ 50,00 deve resultar na cobrança de R$ 50,00 e no registro de um alerta de divergência. |

### 11.2 Vulnerabilidades catalogadas

Nomear a falha em um catálogo tem uma utilidade prática: o problema deixa de ser "uma coisa que pode dar errado no nosso sistema" e passa a ser um tipo conhecido, com correção documentada e com outros casos publicados.

| Risco | Vulnerabilidade ou categoria | Referência utilizada | Relação com o Comer-Tchê! |
| :--- | :--- | :--- | :--- |
| **R10** | Controle de acesso quebrado em nível de função, isto é, a API não confere o cargo de quem chama | • **OWASP Top 10:** A01:2021 Broken Access Control<br>• **CWE-285:** Improper Authorization<br>• **OWASP ASVS:** V4.1 Access Control | Acontece quando o aplicativo esconde o botão do painel administrativo, mas a API, chamada diretamente, não verifica se quem está pedindo é administrador. |
| **R07** | Referência direta insegura a objeto, conhecida como IDOR ou BOLA: o usuário troca na requisição o identificador do recurso e recebe o de outra pessoa | • **OWASP API Top 10:** API1:2023 Broken Object Level Authorization<br>• **CWE-639:** Authorization Bypass Through User-Controlled Key | Acontece quando a API usa identificadores em sequência na URL (`/orders/1001`), o que permite somar 1 e ler o pedido, o endereço e o telefone de outro cliente. |
| **R01** | Autenticação fraca e reuso de credenciais vazadas, prática conhecida como *credential stuffing* | • **OWASP Top 10:** A07:2021 Identification and Authentication Failures<br>• **CWE-307:** Improper Restriction of Excessive Authentication Attempts | Permite que um atacante teste, em massa, listas de senhas vazadas de outros sites, aproveitando a ausência de segundo fator e de verificação do contexto de acesso. |
| **R03 / R04** | Adulteração de parâmetro e falha de integridade de dados: o sistema confia em um valor que o cliente podia alterar | • **OWASP Top 10:** A08:2021 Software and Data Integrity Failures<br>• **CWE-472:** External Control of Assumed-Immutable Web Parameter | Acontece quando o servidor aceita o preço enviado pelo aplicativo, ou quando aceita uma notificação de pagamento sem conferir a assinatura que comprova que ela veio do gateway. |

Os quatro requisitos e os quatro mapeamentos superam o mínimo de três exigido pelo enunciado. A escolha recaiu sobre os riscos de maior alcance da seção 8.6: R10 e R07, que expõem toda a base de clientes de uma vez, R01, o mais provável no dia a dia, e o par R03/R04, que causa prejuízo financeiro contínuo.
