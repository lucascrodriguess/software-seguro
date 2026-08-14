## 20. Pipeline DevSecOps

### 20.1 O que muda em relação a um ciclo tradicional

Um *pipeline* de desenvolvimento é a sequência de etapas pela qual uma alteração de software passa desde a ideia até entrar em produção. No modelo tradicional, a segurança aparece no fim dessa sequência, como uma revisão antes de publicar, ou depois dela, quando um incidente obriga a voltar atrás.

DevSecOps é a prática de distribuir a segurança por toda a sequência, em vez de concentrá-la em um ponto. Duas consequências práticas disso aparecem neste trabalho:

- **Cada momento produz uma evidência.** Não basta afirmar que a análise foi feita: ela deixa um artefato verificável, como a tabela de ameaças, o registro de riscos ou o relatório da ferramenta de varredura.
- **Cada momento tem uma condição de passagem.** Se a evidência não atende ao critério, a alteração não avança. Essas condições são o que impede que a pressa de entregar atropele uma falha conhecida.

O trabalho inteiro desta disciplina é, na prática, uma passagem por esse pipeline. As seções 5 a 19 são as evidências de cada momento, e a tabela a seguir organiza essa correspondência.

### 20.2 O pipeline proposto para o Comer-Tchê!

| Momento | Atividade de segurança | Evidência produzida | Condição para continuar |
| ------- | ---------------------- | ------------------- | ----------------------- |
| **Planejamento** | Modelagem de ameaças com STRIDE e escrita dos casos de abuso | Tabela com 11 ameaças (T01 a T11) e 5 casos de abuso, nas seções 5 a 7 | As seis categorias do STRIDE analisadas, e cada ameaça ligada a um ativo concreto |
| **Análise de risco** | Cálculo, priorização e tratamento dos riscos com o NIST CSF 2.0 | Registro de 11 riscos com probabilidade, impacto e nível; plano de tratamento, ordem de implementação e residual estimado, nas seções 8 a 10 | Todo risco crítico com estratégia, responsável e forma de verificação definidos |
| **Requisitos e arquitetura** | Derivação de requisitos verificáveis e registro das decisões de arquitetura | RS01 a RS04 com critério de verificação, quatro decisões e o diagrama da arquitetura, nas seções 11 a 14 | Cada risco crítico com pelo menos um requisito verificável e uma decisão que o trata |
| **Implementação segura** | Escolha de práticas de código seguro, com os testes escritos antes do código | Pseudocódigo do controle de autorização e da validação de entrada, com os testes TS01 a TS08, nas seções 15 a 17 | Testes definidos antes da implementação, cobrindo um caso válido e um caso malicioso |
| **Testes automatizados** | Execução de TS01 a TS09 a cada alteração enviada ao repositório | Job `C1` do pipeline no [repositório do sistema](https://github.com/comer-tche/comer-tche/actions), que sobe a aplicação e executa os nove testes | Todos os testes de autorização e de integridade aprovados |
| **Análise de código e dependências** | Varredura do histórico em busca de segredo versionado e análise das bibliotecas de terceiros (SCA, que compara as versões usadas com listas públicas de vulnerabilidades) | Jobs `C2` (gitleaks) e `C3` (`npm audit`) do pipeline | Nenhum segredo no repositório e nenhuma vulnerabilidade de severidade alta ou crítica em aberto |
| **Teste dinâmico** | Varredura da aplicação em execução com o OWASP ZAP | Relatório, capturas de tela e hashes em `evidencias/etapa-5/`, com três achados analisados, na seção 18; e o job `C4`, que repete a varredura contra o próprio sistema a cada alteração | Achados de severidade média ou superior analisados, com correção proposta ou descarte justificado |
| **Implantação** | Publicação da versão aprovada em produção | *Atividade proposta.* Ainda não executada | Aprovação em todos os momentos anteriores, e nenhum segredo presente no código ou no repositório |
| **Monitoramento e resposta** | Registro de eventos, regras de detecção e fluxo de resposta a incidentes | Três regras de detecção e o fluxo de quatro fases após o alerta, na seção 19 | Regras ativas sobre os eventos previstos no controle de registro do risco R06 |

### 20.3 Condições que impedem a continuidade

As quatro condições abaixo interrompem o pipeline. Nenhuma delas é genérica: cada uma existe por causa de um risco específico do registro da seção 8.4.

**C1. Teste de autorização reprovado.** Se TS02 ou TS04 falharem, a alteração não avança. São os testes que verificam se um cliente comum consegue chamar a API administrativa e se um cliente consegue ler o pedido de outro. Eles cobrem R10 e R07, os dois riscos de maior alcance do registro, porque uma única verificação ausente no servidor expõe a base inteira de clientes de uma vez, e não uma vítima por vez.

**C2. Segredo encontrado no repositório.** Senha, chave ou token versionado por engano interrompe o pipeline e obriga a revogar a credencial antes de qualquer outra coisa. A Decisão 2 da arquitetura depende de um segredo compartilhado com o gateway de pagamento para validar a assinatura das confirmações. Se esse segredo vaza, qualquer pessoa consegue forjar uma confirmação válida, e o R04 volta ao nível que tinha antes do controle.

**C3. Dependência com vulnerabilidade conhecida de severidade alta ou crítica.** Uma biblioteca de terceiros vulnerável anula controles implementados corretamente no nosso código, porque o atacante deixa de precisar da nossa falha e passa a usar a dela. Vale lembrar a premissa da primeira aula: basta uma vulnerabilidade para o sistema ser considerado inseguro, e ela não precisa ter sido escrita por nós.

**C4. Achado de severidade alta na varredura dinâmica sem análise registrada.** O objetivo não é zerar o relatório da ferramenta, e sim garantir que ninguém publique com um achado relevante ignorado. Um alerta pode ser descartado como falso positivo, como aconteceu com o Timestamp Disclosure na seção 18.5, desde que o motivo do descarte esteja escrito.

### 20.4 Do pipeline proposto ao pipeline em execução

O pipeline descrito aqui deixou de ser apenas uma proposta. O sistema foi implementado no repositório [comer-tche](https://github.com/comer-tche/comer-tche), e as quatro condições da seção 20.3 são hoje quatro jobs que rodam a cada alteração enviada.

A ordem dos commits daquele repositório reproduz a ordem desta análise, e é o que sustenta a afirmação de que a segurança veio antes do código: o primeiro commit é a aplicação sem nenhum controle, com as falhas correspondentes a R01, R03, R04, R07 e R10 marcadas no próprio código, e cada commit seguinte aplica um controle citando a seção que o originou, seguindo a ordem de implementação da seção 9.5.

Uma decisão de arquitetura nasceu da implementação e merece registro, porque contradiz a leitura literal da Decisão 1. O sistema foi feito em Next.js, cujo mecanismo de middleware parece o lugar natural para a validação centralizada de autorização. Ele não é. A CVE-2025-29927, de severidade 9.1, permitia pular o middleware inteiro com um cabeçalho, e sua descrição oficial afirma que a falha atinge quem faz a checagem de autorização ali. O CWE dessa vulnerabilidade é o CWE-285, exatamente o que a seção 11.2 havia mapeado para o R10. A centralização exigida pela Decisão 1 foi obtida por um módulo único chamado dentro de cada rota, e o middleware do framework não decide acesso.

Um momento continua sem execução: a **implantação**, porque não há ambiente de produção, e publicar na internet um sistema cujo primeiro commit é deliberadamente vulnerável não seria adequado.

### 20.5 Integração entre as etapas

O pipeline mostra que as etapas não são sete trabalhos separados, e sim um único ciclo. Um mesmo defeito de projeto pode ser rastreado da primeira à última seção: a ameaça T09, de um cliente comum chamar a API administrativa, vira o risco R10 e recebe pontuação 12 na Etapa 2, gera o requisito RS01 e a Decisão 1 na Etapa 3, vira a Prática 1 com os testes TS01 e TS02 na Etapa 4, e termina como a terceira regra de detecção na Etapa 6, que alerta quando uma conta acumula respostas de acesso negado. É o mesmo problema atravessando o ciclo inteiro, tratado de uma forma diferente em cada momento.

O ciclo também se fecha para trás. A varredura da Etapa 5 apontou três problemas de configuração que a modelagem da Etapa 1 não previa, porque o nosso STRIDE olhou identidade, dados e permissões, mas não cabeçalhos de resposta nem injeção no navegador. Em um pipeline real, esse resultado retorna ao momento de planejamento e origina ameaças novas, que percorrem o ciclo de novo. Segurança não é uma etapa concluída, é um ciclo que recomeça a cada alteração do sistema.
