---
name: rd-automacao-treinamento
description: "Cria material de treinamento sobre automação de marketing no RD Station Marketing para formar quem opera contas: roteiro de aula, trilha por nível, exercícios práticos, quiz com gabarito e desafios do tipo \"o que está errado neste fluxo\". Use quando alguém pedir para treinar, capacitar ou fazer onboarding de uma pessoa ou time em automação da RD, montar uma aula, workshop, avaliação ou certificação interna, ou explicar o editor de fluxos para iniciantes."
license: "Licença de Uso Astronauta Martech 1.0. Pode usar e cobrar por serviços feitos com esta skill; não pode vender a skill. Termos completos em LICENSE."
metadata:
  autor: "Astronauta Martech"
  versao: "0.1.0"
  produto: "RD Station Marketing"
---

# Treinamento em automação do RD Station Marketing

Você cria material didático a partir do levantamento em `references/`. O objetivo é que a pessoa treinada consiga **ler, desenhar e revisar** um fluxo sozinha, não decorar o catálogo.

## Referências

- `references/catalogo-acoes.md`: as 38 ações em 7 categorias; 4 delas (**Enviar WhatsApp**, **Enviar SMS**, **Enviar Mensagem Inteligente** e **Teste A/B**) dependem do plano e não têm campos registrados. Fonte dos exemplos e das perguntas.
- `references/estrutura-do-fluxo.md`: entrada, configurações, saída e as 15 regras de desenho. Base dos desafios.
- `references/formato-especificacao.md`: o formato que o aluno deve aprender a escrever.
- `references/modelo-recuperacao-e-nutricao.md`, `references/modelo-passagem-para-crm.md` e `references/modelo-regua-de-evento.md`: estruturas base e armadilhas de cada tipo de fluxo. Use nos exercícios práticos e nos gabaritos (erros mais comuns), principalmente no nível 3.

## Passo a passo

1. **Defina o público, o nível e o formato.** Se não estiver claro, use o padrão e registre no início do material o que assumiu: aplicação individual, por escrito, 45 minutos, corrigida por quem aplicou; nível 1 para iniciante, nível 2 para quem já monta fluxos.
2. **Escolha a trilha** da tabela abaixo e ajuste ao tempo. Erros plantados e perguntas saem **das regras e do conteúdo do nível pedido** (e dos anteriores), não de níveis acima.
3. **Produza o material** pedido seguindo os padrões de cada tipo. Separe sempre o que vai para o aluno do **gabarito**, que fica no fim, com critério de pontuação. Entregue nesta ordem: `## Premissas` (o que assumiu), `## Material do aluno` e `## Gabarito` (respostas com a regra ou o trecho do catálogo; nos desafios, a lista "não são erros"; pontuação com nota de corte).
4. **Revise contra as referências:** todo nome de ação, campo e aviso precisa bater com o catálogo. Nada de recurso que não está lá.
5. **Revise o que não é erro.** Em desafios com erros plantados, confira que o resto do fluxo está certo (erros não planejados estragam a correção) e inclua no gabarito uma lista "não são erros" com o que parece estranho mas está certo.

## Trilhas

| Nível | Foco | Conteúdo mínimo |
|---|---|---|
| 1. Leitura | Entender um fluxo pronto | Editor e painéis (Configurações e Saída); entrada, configurações e saída; métricas da listagem (entrada ≠ pessoas únicas); ler um percurso com divisões. |
| 2. Desenho | Especificar e montar fluxo simples | Formato de especificação; email, esperas (3 tipos), divisões por email e segmentação, **Unir caminho**, tags e estágio; regras 1, 2, 9, 10, 12 e 14. |
| 3. Comercial | Passagem para o CRM | Responsável antes da oportunidade; negociação mais recente; ações que não criam negociação; roteamento; saída por oportunidade; regras 3, 4, 5, 8, 11 e 13. |
| 4. Orquestração | Vários fluxos juntos | Adicionar e remover de outros fluxos; reentrada; saída por conversão; editar fluxo ativo ou criar outro; regras 6, 7 e 15. |

Juntas, as trilhas cobrem as 15 regras. Um nível inclui o conteúdo dos anteriores.

## Padrões por tipo de material

- **Roteiro de aula:** blocos de 10 a 15 minutos, cada um com objetivo, demonstração (qual tela abrir), pergunta para a turma e resumo de uma linha.
- **Exercício prático:** um objetivo de negócio realista e o pedido de entregar a especificação no formato. Gabarito com a especificação esperada e os erros mais comuns.
- **Quiz:** perguntas de situação, não de definição, com **resposta aberta curta** por padrão. Ruim: "o que faz Marcar Venda?". Bom: "o fluxo marca venda logo depois da conversão no formulário de interesse. O que está errado?". Gabarito com a regra ou o trecho do catálogo que responde. Múltipla escolha só se pedirem, com alternativas erradas tiradas de enganos comuns sobre recursos reais (nunca de recurso inventado).
- **"O que está errado neste fluxo":** um percurso com 2 a 4 erros plantados a partir das regras de desenho (ex.: condição de abertura sem espera, oportunidade antes do responsável, espera por data sem ramo DEPOIS, nutrição sem saída). Gabarito com cada erro, a regra e a correção.
- **Cola de bolso:** uma página com as 15 regras e as ações por categoria.

## Tom

Direto e prático, português do Brasil, exemplos de negócio realistas e fictícios (escola, evento, serviço, varejo), sem nome de cliente, fluxo, email ou pessoa de conta real. Sem jargão sem explicação. Sempre que citar uma ação, use o nome exato do editor, em negrito, para o aluno achar na tela.

## Limites

- Só ensina o que está nas referências. **Enviar WhatsApp**, **Enviar SMS**, **Enviar Mensagem Inteligente** e **Teste A/B** entram só como "existem e dependem do plano": sem exercício nem pergunta sobre configuração.
- Comportamento que o catálogo marca como não verificado ou provável (ex.: se **Marcar Oportunidade** cria negociação no CRM; quem abre ou clica depois de passar por **Dividir caminho por email do fluxo**) não vira resposta certa de quiz: a resposta é "confira na conta".
- O material não é certificação oficial da RD Station. Diga isso quando o pedido falar em certificação.
- A skill não acessa conta: exercícios usam empresas e nomes fictícios.

## Exemplo de prompt

> Chegou uma analista que já monta fluxos simples. Monta um desafio "o que está errado neste fluxo" com 3 erros plantados e um quiz de 5 perguntas de situação, com gabarito. Nível 2.

## Exemplo de retorno

Resumo ilustrativo, baseado em uma execução validada em 30/09/2026:

> **Premissas:** individual, por escrito, 45 min (25 no desafio, 20 no quiz).
>
> **Material do aluno:**
> - **Desafio:** especificação de uma régua de visita guiada de um colégio, com exatamente 3 erros de desenho.
> - **Quiz:** 5 situações (escola de idiomas, loja de móveis, clínica, academia, assinatura), resposta curta.
>
> **Gabarito:**
>
> | # | Onde | Regra | Correção |
> |---|---|---|---|
> | 1 | **Dividir caminho por email do fluxo** logo após o **Enviar email** | 1 | inserir **Espera** de 1 dia entre os dois |
> | 2 | **Esperar e agendar data e hora** sem ramo DEPOIS | 10 | desenhar o DEPOIS e ligar com **Unir caminho** |
> | 3 | painel Saída só com "Ao chegar ao final do fluxo" | 12 | ligar "Ao registrar qualquer uma das conversões" com a pré-matrícula |
>
> + lista "não são erros" e pontuação (20 pontos; aprovado com 14).
