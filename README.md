# Sistema de delivery Comer-Tchê!

Trabalho da disciplina de Engenharia de Software Seguro. O documento está dividido em
arquivos, um por seção, e o número do arquivo é o número da seção.

## Índice

**Etapa 1 — Casos de abuso e modelagem de ameaças com STRIDE**

1. Identificação do sistema — nesta página, abaixo
2. Descrição do sistema — nesta página, abaixo
3. [Usuários, ativos e pontos de interação](<3. Usuarios, ativos e pontos de interacao.md>)
4. [Visão geral do fluxo](<4. Visao geral do fluxo.md>)
5. [Modelagem de ameaças com STRIDE](<5. Modelagem de ameacas STRIDE.md>)
6. [Casos de abuso](<6. Casos de abuso.md>)
7. [Considerações finais](<7. Consideracoes finais-E1.md>)

**Etapa 2 - Análise, priorização e tratamento de riscos com o NIST CSF 2.0**

8. [Análise e priorização de riscos](<8. Analise e priorizacao de riscos.md>)
9. [Tratamento dos riscos com o NIST CSF 2.0](<9. Tratamento dos riscos com o NIST CSF.md>)
10. [Considerações finais](<10. Consideracoes finais-E2.md>)

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
