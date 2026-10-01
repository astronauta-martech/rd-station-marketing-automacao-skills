---
name: rd-automacao-regua-de-evento
description: "Desenha a régua de automação de um evento com data marcada no RD Station Marketing (webinar, live, aula aberta, aula magna, workshop, evento presencial), da inscrição ao pós-evento: confirmação, lembretes por data e hora, tratamento de quem se inscreve atrasado, presentes e ausentes, gravação e próximo passo comercial. Use quando alguém pedir fluxo, régua, sequência ou automação para um evento, inscrição, lembrete de evento ou pós-evento. Entrega uma especificação pronta para montar."
license: "Licença de Uso Astronauta Martech 1.0. Pode usar e cobrar por serviços feitos com esta skill; não pode vender a skill. Termos completos em LICENSE."
metadata:
  autor: "Astronauta Martech"
  versao: "0.1.0"
  produto: "RD Station Marketing"
---

# Régua de evento

Você adapta o modelo de `references/modelo-regua-de-evento.md` ao evento da pessoa e entrega uma especificação no formato de `references/formato-especificacao.md`.

## Referências

- `references/modelo-regua-de-evento.md`: parâmetros, estrutura base, variações, armadilhas e métricas. **Leia primeiro.**
- `references/formato-especificacao.md`: formato de saída.
- `references/catalogo-acoes.md`: nomes e campos exatos das ações (principalmente **Esperar e agendar data e hora** e **Unir caminho**).
- `references/estrutura-do-fluxo.md`: entrada, recorte, saída e regras de desenho (preenchem "Regras conferidas").

Se houver conector do RD Station Marketing:
- `landing_pages_search` e `forms_search` (parâmetro `search`) para o identificador da conversão de inscrição. Inscrição feita por API não aparece ali: se não achar, pergunte o identificador.
- `segmentation_list` para a lista de presentes do passo 6. Ela pode não trazer todas as segmentações: lista ausente é "conferir", não "não existe".
- `workflows_search` para fluxos com nome parecido com o do evento. Ele devolve só nome e status, não a entrada: escreva "(pelo nome)" e deixe a conferência da entrada no editor em Decisões em aberto.

Sem conector, marque essas dependências como "conferir".

## Passo a passo

1. **Colete os parâmetros** da tabela do modelo. Os indispensáveis: data e hora do evento, como a pessoa se inscreve, onde está o acesso. Pergunte o que faltar, no máximo 5 perguntas, sempre com sugestão.
2. **Decida o recorte da entrada.** "Leads que já atendem aos critérios" só se o identificador de conversão for exclusivo deste evento e a inscrição já estiver aberta. Se a landing page ou o formulário vem de um evento anterior, "já atendem" manda a confirmação e os lembretes para todos os inscritos antigos: use "Leads que vão atender aos critérios" e registre em Decisões em aberto como tratar quem já se inscreveu neste evento (ex.: opção **Inserir Leads no fluxo**, no menu do fluxo na listagem).
3. **Escolha a variação:** online, presencial, pago, série de aulas, com ou sem lista de presença, com ou sem WhatsApp. **Enviar WhatsApp** depende do plano: entra em Dependências como "conferir", e o lembrete por email fica desenhado como alternativa.
4. **Calcule as datas.** Converta "véspera", "1 hora antes" e "dia seguinte" em datas e horas absolutas (DD/MM/AAAA às HH:MM). Diga o fuso considerado e lembre que o disparo usa o fuso da conta. Depois compare cada data com a data prevista de ativação (sem ela, use a de hoje e diga isso): o calendário da RD não aceita data passada, então a espera cuja data já terá passado sai do percurso junto com o lembrete dela, o caminho segue direto para a próxima espera, e isso vai para Decisões em aberto.
5. **Monte o percurso** a partir da estrutura base, com as uniões explícitas e os rótulos de trecho que ela usa. Toda **Esperar e agendar data e hora** precisa dos dois ramos (`ATÉ` e `DEPOIS`) desenhados; ramo que nunca é percorrido na prática ganha `(nada: motivo)`. Inclua a tabela "Quem recebe o quê" com os quatro momentos de inscrição: antes da véspera, entre a véspera e 1 hora antes, com o evento começando ou passado, depois do pós-evento.
6. **Pós-evento:** só divida presentes e ausentes se existir uma segmentação ou conversão de presença. Sem isso, faça um pós-evento único com **texto neutro** (não supõe que a pessoa assistiu) e registre em **Decisões em aberto**.
7. **Tag do evento:** `evento-<aaaa-mm-dd>-<tema-curto>-inscrito`, em minúsculas, sem acento, até umas 40 letras.
8. **Confira as armadilhas** do modelo, uma a uma, e marque em **Regras conferidas**.
9. **Entregue** a especificação e, logo abaixo, uma linha do tempo simples, com as datas calculadas no passo 4:
   `Inscrição > Confirmação imediata > Véspera DD/MM/AAAA 10:00 > Dia DD/MM/AAAA 18:00 > Evento 19:00 > Dia seguinte DD/MM/AAAA 10:00`

## Limites

- Não escreva o conteúdo dos emails; indique o objetivo de cada um em uma linha. Se pedirem o texto, faça depois, separado da especificação.
- O calendário da RD não aceita data passada. As datas intermediárias são conferidas no passo 4; se o próprio evento já passou, avise antes de desenhar.
- A especificação não cria nada na conta. Para montar, use `rd-automacao-montar`.

## Exemplo de prompt

> Live "Planejamento de captação" dia 12/11 às 19h (Brasília), no YouTube. Inscrição pela landing page. O link vai na confirmação. Não temos lista de presença. Monta a régua.

## Exemplo de retorno

Trecho de uma execução de teste de 30/09/2026 (pedido fictício, nomes simplificados). Resumo, Configurações, Saída, Dependências e Regras conferidas ficaram de fora para caber aqui; na entrega, siga o formato inteiro.

> ## Entrada
> - **Tipo:** conversão em evento
> - **Detalhe:** landing page da live (pendente: identificador da conversão)
> - **Recorte:** leads que vão atender
>
> ## Percurso
> 1. **Adicionar Tags**: evento-2026-11-12-planejamento-inscrito
> 2. **Enviar email**: "Live 12/11 - Confirmação" (com o link do YouTube)
> 3. **Esperar e agendar data e hora**: 11/11/2026 às 10:00
>    - **3.ATÉ**
>      1. **Enviar email**: "Live 12/11 - Véspera"
>      2. **Unir caminho** → L1.1
>    - **3.DEPOIS** [L1: lembrete de 1 hora]
>      1. **Esperar e agendar data e hora**: 12/11/2026 às 18:00
>         - **L1.1.ATÉ**
>           1. **Enviar email**: "Live 12/11 - Começa em 1 hora" (com o link)
>           2. **Unir caminho** → P1.1
>         - **L1.1.DEPOIS** [P1: pós-evento]
>           1. **Esperar e agendar data e hora**: 13/11/2026 às 10:00
>              - **P1.1.ATÉ** [P2: email do pós]
>                1. **Enviar email**: "Live 12/11 - Pós" (texto neutro, com a gravação e o próximo passo)
>              - **P1.1.DEPOIS**
>                1. **Espera**: 1 dia, 0 hora, 0 min
>                2. **Unir caminho** → P2.1
>
> ## Decisões em aberto
> - Identificador da conversão da landing page.
> - A inscrição já está aberta? Se sim e a landing page for exclusiva da live, trocar o recorte para "já atendem".
> - Sem lista de presença: pós-evento único, com texto neutro e a gravação.
>
> ## Linha do tempo
> Inscrição > confirmação imediata > véspera 11/11/2026 10:00 > dia 12/11/2026 18:00 > live 19:00 > pós 13/11/2026 10:00 (horário de Brasília; o disparo usa o fuso da conta)
>
> ## Quem recebe o quê
> | Inscrição | Recebe |
> |---|---|
> | Antes da véspera | confirmação, véspera, 1 hora antes, pós |
> | Entre a véspera e 1 hora antes | confirmação, 1 hora antes, pós |
> | Com a live começando ou passada | confirmação (com o link), pós |
> | Depois do pós-evento | confirmação e, 1 dia depois, o pós |
