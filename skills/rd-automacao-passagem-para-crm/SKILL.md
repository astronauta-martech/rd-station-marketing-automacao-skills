---
name: rd-automacao-passagem-para-crm
description: "Desenha a passagem de leads do RD Station Marketing para o RD Station CRM por automação: distribuição de responsáveis, marcação de oportunidade, criação de negociação no funil e etapa certos, nome, anotação, tarefa de primeiro contato, produto e roteamento por produto, qualificação ou equipe. Use quando alguém falar em mandar lead pro CRM, pro comercial ou pro vendedor, criar negociação automaticamente, distribuir leads, rodízio de vendedores, SLA de primeiro contato ou handoff marketing-vendas. Entrega especificação pronta para montar e aponta riscos de duplicidade e de negociação sem dono."
license: "Licença de Uso Astronauta Martech 1.0. Pode usar e cobrar por serviços feitos com esta skill; não pode vender a skill. Termos completos em LICENSE."
metadata:
  autor: "Astronauta Martech"
  versao: "0.1.0"
  produto: "RD Station Marketing e RD Station CRM"
---

# Passagem do Marketing para o CRM

Você desenha o trecho de automação que entrega o lead ao time comercial com dono, contexto e tarefa, e entrega uma especificação no formato de `references/formato-especificacao.md`.

## Referências

- `references/modelo-passagem-para-crm.md`: parâmetros, estrutura base, variações de roteamento, as 9 armadilhas e as dependências que o conector não confere. **Leia primeiro.**
- `references/catalogo-acoes.md`: seções RD Station CRM e Responsável e Notificação, e **Marcar Oportunidade** (em Gerenciar Lead: saída por oportunidade e risco de negociação dupla).
- `references/formato-especificacao.md`: formato de saída.
- `references/estrutura-do-fluxo.md`: saída, reentrada e regras de desenho (preenchem "Regras conferidas").
- `references/conector-rd-marketing.md` e `references/codigos-de-acao.md`: o que o conector mostra (e não mostra) de um fluxo, limites de chamada e tradução dos códigos de ação. **Leia antes de chamar qualquer ferramenta de fluxo.**
- `references/disciplina-de-evidencia.md`: como escrever números, níveis de certeza e recomendações que mexem em fluxo. **Aplique antes de entregar.**

## Passo a passo

1. **Defina o gatilho de "pronto pra conversa"**: a entrada do fluxo. Se a pessoa não souber, proponha a conversão de pedido de contato (com conector: `forms_search` e `landing_pages_search` mostram o identificador). Se o pedido é **redesenhar um fluxo que já existe**, ache-o com `workflows_search` e use `workflow_get_details` só para saber quais tipos de ação ele tem (códigos de 5 letras, como `MKOPP` para **Marcar Oportunidade** e `CRMOP`, provável, para **Criar Negociação no CRM**; tradução em `references/codigos-de-acao.md`). O conector não mostra entrada, funil, etapa, responsável, ramos nem a ordem das ações: esses dados vêm da tela do editor ou da pessoa, e o que faltar vai para **Decisões em aberto**. Se der 429, siga a regra 6 de `references/disciplina-de-evidencia.md`. Faça uma especificação de edição e decida com a pessoa entre editar e substituir (regra 15).
2. **Colete o destino no CRM:** funil, etapa, pessoa ou equipe, prazo do primeiro contato, produto (se houver).
3. **Confira o destino, se houver conector do CRM.** Com o conector do RD Station CRM disponível, liste funis (`funnel_list`), etapas de cada funil usado (`funnel_stages_list`, que exige o id do funil), equipes (`teams_list`), usuários (`users_list`) e produtos (`products_list`), e marque cada dependência como "existe" ou "criar". Use os nomes exatamente como estão lá. `users_list` mostra usuários do CRM, não do Marketing: a pessoa de **Distribuir Leads entre os responsáveis** ou **Alterar responsável pelos Leads** se confere na tela. Sem conector do CRM, marque as dependências como "conferir na tela" e liste em **Decisões em aberto**.
4. **Monte o percurso a partir da estrutura base**, nesta ordem obrigatória:
   1. responsável (**Distribuir Leads entre os responsáveis** ou **Alterar responsável pelos Leads**);
   2. **Marcar Oportunidade** (antes, veja a armadilha 9: se na conta a marcação já cria negociação no CRM, não some **Criar Negociação no CRM**; sem essa confirmação, registre o risco de negociação dupla em **Decisões em aberto**);
   3. **Criar Negociação no CRM**;
   4. só então as ações que atuam na negociação mais recente (nome, anotação, tarefa, produto, divisões, mover, status).
5. **Decida a reentrada de propósito.** Se "mais de uma vez", marque "atribuir ao último responsável" e registre o risco de negociação repetida em **Decisões em aberto**.
6. **Escreva nome e anotação que façam sentido com variável vazia** (a RD omite variável sem valor).
7. **Roteamento:** use divisões por produto, qualificação ou equipe só se houver regra de negócio clara. Qualificação é valor exato; "4 ou mais" vira duas divisões. Toda negociação nasce com qualificação 1: **Dividir caminho por qualificação** logo depois de **Criar Negociação no CRM** sempre dá 1. Use só depois de uma espera em que alguém qualifica a negociação, ou num fluxo com entrada "Eventos CRM" (Negociação atualizada).
8. **Painel Saída:** desligue "Ao receber marcação Oportunidade" (senão o lead sai antes da negociação).
9. **Preencha Regras conferidas** com as regras de `references/estrutura-do-fluxo.md` que se aplicam (no mínimo 3, 4, 8, 11, 12 e 13; 5 e 14 se houver roteamento; 15 se for redesenho) e confira ali também as 9 armadilhas do modelo. Depois entregue.

## Padrões quando a pessoa não responde

| Decisão | Padrão |
|---|---|
| Quem atende | Uma pessoa fixa nos dois blocos (lead e negociação), até existir fila definida |
| Funil e etapa | O funil padrão do CRM (o que a conta usa como principal), primeira etapa; sem um funil claro, vai para **Decisões em aberto** |
| Reentrada | Apenas uma vez, como na estrutura base. A escolha vai sempre para **Decisões em aberto**, com o risco de cada lado: negociação repetida (mais de uma vez) ou lead que pede contato de novo e é ignorado (apenas uma vez) |
| Prazo da tarefa | Ligar, 2 horas, com "não marcar aos fins de semana" ligado |

Registre cada padrão usado em `## Padrões aplicados` na especificação.

## Limites

- A skill não cria funil, etapa, equipe ou produto no CRM; aponta como dependência.
- Não recomende **Atualizar status**, **Mover Negociação no CRM** ou **Atualizar responsável** como forma de criar negociação: elas não criam.
- A especificação não cria nada na conta. Para montar, use `rd-automacao-montar`.

## Exemplo de prompt

> Hoje o fluxo de pedido de contato do site cria negociação e marca oportunidade quando alguém pede contato. Quero redesenhar a passagem pro CRM: com dono, tarefa de ligar em 2 horas e anotação com contexto. Confere no CRM quais funis e equipes existem.

## Exemplo de retorno

Resumo ilustrativo, baseado em uma execução validada em 30/09/2026 (nomes e contagens alterados):

> **Situação atual (conector):** o fluxo tem **Marcar Oportunidade** e, provável, **Criar Negociação no CRM**. Ordem, funil e responsável não aparecem no conector: conferir no editor.
>
> **No CRM:** 4 funis (1 marcado como fora de uso), 3 equipes (nenhuma configurada como fila), 8 usuários, 12 produtos. Alerta (provável): a última edição do fluxo atual é anterior à criação de todos os funis que existem hoje, então o **Criar Negociação no CRM** dele pode apontar para um funil apagado. Confira no editor.
>
> 1. **Alterar responsável pelos Leads**: <pessoa>
> 2. **Marcar Oportunidade**
> 3. **Criar Negociação no CRM**: Funil de vendas > primeira etapa, por pessoa: <mesma pessoa do passo 1>, "atribuir ao último responsável" marcado
> 4. **Atualizar nome da Negociação**: "*|PRIMEIRO_NOME|* pediu contato pelo site"
> 5. **Adicionar anotação**: assunto, objetivo e mensagem do formulário
> 6. **Criar tarefa na Negociação**: Ligar, 2 horas
> 7. **Notificar responsável pelo Lead**
>
> **Saída:** "Ao receber marcação Oportunidade" desligada (senão o lead sai no passo 2).
>
> **Decisões em aberto:** na conta, **Marcar Oportunidade** já cria negociação no CRM? Se criar, sai o passo 3 (negociação dupla). Reentrada: apenas uma vez (padrão) ou mais de uma, com risco de negociação repetida. Editar o fluxo atual ou criar outro e desativar o antigo (regra 15).
