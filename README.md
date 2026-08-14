# Sistema de delivery Comer-Tchê!

Trabalho da disciplina de Engenharia de Software Seguro. O conteúdo está dividido em
arquivos numerados por ordem de leitura. Cada arquivo reúne uma ou mais seções do
documento, e a numeração das seções é contínua de 1 a 19, como indicado abaixo.

## Índice

**Etapa 1 — Casos de abuso e modelagem de ameaças com STRIDE**

- Seções 1 e 2: identificação e descrição do sistema, nesta página, abaixo
- Seção 3: [Usuários, ativos e pontos de interação](<1. Usuarios, ativos e pontos de interacao.md>)
- Seção 4: [Visão geral do fluxo](<2. Visao geral do fluxo.md>)
- Seções 5 a 7: [Modelagem de ameaças com STRIDE, casos de abuso e considerações finais](<3. E1. Modelagem de ameacas e Casos de abuso.md>)

**Etapa 2 — Análise, priorização e tratamento de riscos com o NIST CSF 2.0**

- Seção 8: [Análise e priorização de riscos](<4. Analise e priorizacao de riscos.md>)
- Seções 9 e 10: [Tratamento dos riscos com o NIST CSF 2.0 e considerações finais](<5. E2. Tratamento dos riscos com o NIST CSF.md>)

**Etapa 3 — Projeto de uma arquitetura segura**

- Seção 11: [Requisitos de segurança e vulnerabilidades catalogadas](<6. Requisitos de seguranca e vulnerabilidades.md>)
- Seções 12 a 14: [Diagrama da arquitetura segura, decisões de arquitetura e considerações finais](<7. E3. Arquitetura segura.md>)

**Etapa 4 — Código seguro e testes de segurança**

- Seções 15 a 17: [Escolha das práticas, controle de autorização e validação de entrada](<8. E4. Codigo seguro e testes de seguranca.md>)

**Etapa 5 — Verificação de vulnerabilidades**

- Seção 18: [Verificação com o OWASP ZAP](<9. E5. Verificacao de vulnerabilidades.md>), com as evidências da execução em [`evidencias/etapa-5/`](evidencias/etapa-5/)

**Etapa 6 — Monitoramento e detecção de intrusões**

- Seção 19: [Regras de detecção e resposta a incidentes](<10. E6. Monitoramento e Deteccao de Intrusoes.md>)

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
