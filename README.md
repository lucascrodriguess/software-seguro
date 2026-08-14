# Sistema de delivery Comer-Tchê!

Trabalho da disciplina de Engenharia de Software Seguro. Os documentos das etapas 1 a 5 estão em `docs/`, os das etapas 6 e 7 em `roteiros/`, e as evidências da verificação em `evidencias/`. A numeração das seções é contínua de 1 a 20, e o índice abaixo indica em qual arquivo cada seção está.

## Índice

**Etapa 1 — Casos de abuso e modelagem de ameaças com STRIDE**

- Seções 1 e 2: identificação e descrição do sistema, nesta página, abaixo
- Seção 3: [Usuários, ativos e pontos de interação](docs/etapa-1-usuarios-ativos-e-pontos-de-interacao.md)
- Seção 4: [Visão geral do fluxo](docs/etapa-1-visao-geral-do-fluxo.md)
- Seções 5 a 7: [Modelagem de ameaças com STRIDE, casos de abuso e considerações finais](docs/etapa-1-ameacas-stride-e-casos-de-abuso.md)

**Etapa 2 — Análise, priorização e tratamento de riscos com o NIST CSF 2.0**

- Seção 8: [Análise e priorização de riscos](docs/etapa-2-analise-e-priorizacao-de-riscos.md)
- Seções 9 e 10: [Tratamento dos riscos com o NIST CSF 2.0 e considerações finais](docs/etapa-2-tratamento-dos-riscos-nist.md)

**Etapa 3 — Projeto de uma arquitetura segura**

- Seção 11: [Requisitos de segurança e vulnerabilidades catalogadas](docs/etapa-3-requisitos-e-vulnerabilidades.md)
- Seções 12 a 14: [Diagrama da arquitetura segura, decisões de arquitetura e considerações finais](docs/etapa-3-arquitetura-segura.md)

**Etapa 4 — Código seguro e testes de segurança**

- Seções 15 a 17: [Escolha das práticas, controle de autorização e validação de entrada](docs/etapa-4-codigo-seguro.md)

**Etapa 5 — Verificação de vulnerabilidades**

- Seção 18: [Verificação com o OWASP ZAP](docs/etapa-5-verificacao-vulnerabilidades.md), com as evidências da execução em [`evidencias/etapa-5/`](evidencias/etapa-5/)

**Etapa 6 — Monitoramento e detecção de intrusões**

- Seção 19: [Regras de detecção e resposta a incidentes](roteiros/etapa-6-deteccao-de-intrusoes.md)

**Etapa 7 — DevSecOps**

- Seção 20: [Pipeline DevSecOps e condições de bloqueio](roteiros/etapa-7-devsecops.md)

## 1. Identificação do sistema

- Nome do sistema: Comer-Tchê!
- Repositório: https://github.com/lucascrodriguess/software-seguro
- Justificativa: O sistema foi escolhido por ser uma aplicação amplamente utilizada, que envolve múltiplos tipos de usuários, dados sensíveis e integrações externas.

### Integrantes e contas do GitHub

Tabela para identificar a autoria dos commits.

| Integrante                    | Conta no GitHub                                                    | Nome nos commits        |
| ----------------------------- | -------------------------------------------------------------------- | ----------------------- |
| Lucas Corrêa Rodrigues        | [@lucascrodriguess](https://github.com/lucascrodriguess)             | lucascrodriguess        |
| Luis Felipe Calone Silveira   | [@Luisxsxsx](https://github.com/Luisxsxsx)                           | Luisxsxsx               |
| Rafael da Silva Moral         | [@Rafaleel](https://github.com/Rafaleel)                             | Rafael da Silva Moral   |
| Cristhian Kapelinski          | [@CristhianKapelinski](https://github.com/CristhianKapelinski)       | Cristhian Kapelinski    |
| Beatriz Machado               | [@INARI18](https://github.com/INARI18)                               | Beatriz Machado         |

## 2. Descrição do sistema

O sistema é uma plataforma de delivery de alimentos que permite aos restaurantes gerenciarem seus cardápios, receberem e atualizarem o status dos pedidos, enquanto os entregadores aceitam solicitações de entrega, visualizam rotas e confirmam a conclusão das entregas. Os clientes podem se cadastrar, realizar login, pesquisar restaurantes, visualizar cardápios, fazer pedidos, efetuar pagamentos e acompanhar a entrega em tempo real. O sistema também conta com um painel administrativo para gerenciar usuários, restaurantes, entregadores, pagamentos e promoções, além de armazenar informações como dados pessoais, histórico de pedidos, endereços e registros das operações.

### O que o sistema não faz

Delimitar o que fica de fora é parte da análise de segurança, porque define o que não precisamos proteger e o que fica sob responsabilidade de terceiros:

- **Não processa o pagamento diretamente.** A cobrança é executada por um gateway externo, e o sistema guarda apenas a referência tokenizada do cartão, nunca o número. Os dados completos do cartão ficam sob responsabilidade do gateway.
- **Não calcula rotas.** A navegação do entregador usa uma API de mapas de terceiros. O sistema envia as coordenadas da entrega e recebe o trajeto pronto.
- **Não gerencia o estoque nem a operação interna do restaurante.** O cardápio informa o que está disponível, mas o controle de insumos, a produção e a gestão de funcionários do restaurante ficam fora do escopo.
- **Não emite documentos fiscais nem faz contabilidade.** O sistema registra as transações para conciliação, mas a emissão de nota e a apuração contábil são de outro sistema.
- **Não oferece troca de mensagens entre usuários.** O contato entre cliente e entregador acontece por telefone, e não por um canal de mensagens dentro da plataforma.
