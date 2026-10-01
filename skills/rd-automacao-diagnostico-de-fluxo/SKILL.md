---
name: rd-automacao-diagnostico-de-fluxo
description: "Diagnostica um fluxo de automação específico do RD Station Marketing pelo conector MCP: decodifica as ações, mede quantos leads entraram, em que ação estão parados, quantos saíram, como foram os emails do fluxo (entrega, abertura, clique, bounce) e aponta onde mexer. Use quando alguém perguntar por que um fluxo não está funcionando, como está a performance de um fluxo, onde os leads param, se os emails de uma automação estão chegando, ou pedir para analisar, revisar ou auditar uma automação pelo nome. Requer o conector do RD Station Marketing."
license: "Licença de Uso Astronauta Martech 1.0. Pode usar e cobrar por serviços feitos com esta skill; não pode vender a skill. Termos completos em LICENSE."
metadata:
  autor: "Astronauta Martech"
  versao: "0.1.0"
  produto: "RD Station Marketing"
  requer: "conector MCP do RD Station Marketing"
---

# Diagnóstico de um fluxo

Você analisa **um** fluxo com os dados do conector e entrega achados com número, hipótese e próximo passo. Leitura pura: **nenhuma chamada que escreva na conta**.

## Referências

- `references/conector-rd-marketing.md`: ferramentas, limites (principalmente o limite por hora de saídas) e o que o conector não mostra. **Leia primeiro.**
- `references/codigos-de-acao.md`: tradução dos códigos das ações.
- `references/estrutura-do-fluxo.md` e `references/catalogo-acoes.md`: regras de desenho e comportamento de cada ação. Leia no passo 7, antes de escrever hipóteses, e cite como "segundo a documentação da skill" (seção 4 de `disciplina-de-evidencia.md`).
- `references/disciplina-de-evidencia.md`: como escrever números, níveis de certeza e recomendações que mexem em fluxo. **Aplique antes de entregar.**

## Passo a passo

```
- [ ] 1. Achar o fluxo
- [ ] 2. Ler a estrutura
- [ ] 3. Medir entradas
- [ ] 4. Medir onde os leads estão
- [ ] 5. Medir os emails
- [ ] 6. Medir saídas (opcional, limitado)
- [ ] 7. Cruzar e concluir
```

1. **Achar:** `workflows_search` com parte do nome. Se vierem vários, mostre a lista curta e pergunte qual. Confirme status (ativo/inativo).
2. **Estrutura:** **uma** chamada a `workflow_get_details` (o orçamento dessa ferramenta é de poucas chamadas por hora; ver `conector-rd-marketing.md`). Traduza os códigos. A lista vem **sem ordem de percurso e sem ramos**: apresente como "ações presentes", não como sequência. Se der 429, aplique a seção 6 de `disciplina-de-evidencia.md` (espere o `cooldown_seconds` devolvido, se for até 120 segundos, e repita uma vez). Se persistir, siga sem a estrutura e diga isso: sem ela não há `action_id`, então o passo 4 fica "não consultado (limite do conector)" e o funil fica só com entradas e emails.
3. **Entradas:** com navegador, o total desde a criação vem da listagem (Entrada de leads). Pelo conector, `workflow_leads_started_list` no período pedido (padrão: últimos 30 dias; se zero, 90; se ainda zero, **desde a data de criação**, para saber quando parou). O `total_count` é o tamanho da página, não o total: conte paginando por **blocos de data com menos de 100 entradas** (ver "Contar entradas" em `conector-rd-marketing.md`), até cerca de 5 blocos. Acima disso, informe as entradas da listagem e marque pessoas distintas como "não consultado (custo)". Pessoas distintas por `contact_id` (com reentrada, entrada não é pessoa; `contact_id` nulo fica fora e é declarado). A resposta traz nome e email: use só para contar, nunca repita no resultado. Para 429, seção 6 de `disciplina-de-evidencia.md`.
4. **Onde estão:** `workflow_leads_at_action_list` com `page_size` 100, **uma página por ação**, no mesmo período, até cerca de 10 ações (a cota dessa ferramenta é pequena). Com mais ações, priorize envios (`SDEMA`, `SDSMS`), divisões (`CDSEG`) e esperas (`WTDEL`, `WTFIX`) e diga quais ficaram sem medir. O `total_count` é o tamanho da página: com menos de 100 registros ele é a contagem; com 100, pagine por blocos de data só nas 3 ações prioritárias. Para a ordem do percurso, compare o **horário** (`action_date`) em que um mesmo lead chegou a cada ação; na falta disso, ordene pela contagem (mais leads primeiro). Diga que a ordem é inferida. O conector não mostra ramos: a diferença entre duas ações pode ser a divisão em SIM e NÃO, não perda. Escreva "queda" só quando nenhuma ação candidata a ramo explicar a diferença; senão, "diferença (pode ser ramo)". Para 429, seção 6 de `disciplina-de-evidencia.md`.
5. **Emails:** `emails_by_workflow_analytics` com o id do fluxo e **o período em que houve entradas** (senão vem vazio). Abertura e clique sobre entregues; bounce sobre o total processado (se a resposta não trouxer esse campo, use entregues + bounces e marque "(calculado)"). Informe o n. Inclua abertura e clique como etapas do funil: em fluxo curto, a perda está depois do envio. Tipo de email importa: confirmação costuma abrir bem mais que nutrição. Se der erro de plano (403), diga e siga.
6. **Saídas:** `workflow_leads_exited_list` só se a pergunta depender disso, com `page_size` 100: uma chamada só (se vierem 100, diga "100 ou mais") (limite de 1 a 12 por hora conforme o plano; ver `conector-rd-marketing.md`).
7. **Cruzar e concluir.** Para cada achado: o número, a hipótese e como confirmar. Referências úteis (regras de `estrutura-do-fluxo.md`, citadas como "segundo a documentação da skill"):
   - entrada zero com fluxo ativo: gatilho quebrado, lista vazia ou evento que não acontece mais. O conector não mostra qual é a entrada: se a pessoa ou o nome do fluxo indicar a página ou o formulário, confira com `landing_pages_search` ou `forms_search` se ainda existe, marcando "(pelo nome)" quando o palpite vier do nome. Conversão que chega de fora dos formulários e landing pages da RD não aparece em nenhuma das duas. Sem pista, pergunte à pessoa ou liste "conferir a entrada no editor";
   - leads da equipe ou de teste na amostra: separe e diga quantos, principalmente em amostra pequena;
   - diferença pequena entre entradas e leads na primeira ação: registre, sem concluir erro;
   - queda grande depois de um `WTFIX` (Esperar e agendar hora ou Esperar e agendar data e hora; o código não distingue): se for data, data passada ou ramo "depois do dia" vazio (regra 10); confirme o tipo no editor;
   - abertura muito baixa: problema de entrega ou assunto; bounce alto: base desatualizada;
   - mesmo email em duas ações de envio (aparece duas vezes em `emails_by_workflow_analytics`): reenvio ou o mesmo email em ramos diferentes; o conector não mostra ramos, confira no editor;
   - ação de CRM (`CRMUT` ou código não mapeado) sem `CRMOP` (Criar Negociação no CRM, mapeamento provável): pode não estar fazendo nada (regra 4).

## Formato de saída

```markdown
# Diagnóstico: <nome do fluxo>
**Status:** <ativo|inativo> · **Criado:** DD/MM/AAAA · **Última edição:** DD/MM/AAAA · **Períodos analisados:** <30 dias, 90 dias, desde a criação: o que foi usado>

## Ações presentes (segundo o conector)
<ações traduzidas; ordem inferida pelos horários, se houver>

## Números (conector, medido: DD/MM a DD/MM)
| Etapa | Leads | Diferença (queda ou ramo?) |
|---|---|---|
| Entraram (entradas / pessoas) | ... | |
| <ação> | ... | ...% |
| Abriram o email <x> | ... | ...% |
| Clicaram | ... | ...% |

**Emails do fluxo:** entregues, abertura, clique, bounce, com o n (ou "indisponível no plano").

## Achados
1. **<achado>**: <número>. Hipótese: <...>. Como confirmar: <...>.

## Próximos passos
- <ação recomendada, em ordem de impacto>

## O que assumi
- <período e por quê; entradas vs pessoas; ordem inferida por horário ou por contagem; leads de teste ou da equipe separados>

## Limites e o que não foi visto
- Não exposto pelo conector: entrada, configurações (reentrada, finais de semana), saída, ramos e a configuração de cada ação. <o que conferir no editor>
- Não consultado: <ações sem medir, saídas, pessoas distintas, com o motivo e o próximo passo>
```

## Limites

- Os dados de leads por fluxo atualizam uma vez por dia: o que aconteceu hoje pode não aparecer.
- Não mostre nome ou email de lead, só contagens, a menos que a pessoa peça um caso específico.
- Nunca ative, desative, edite, duplique ou exclua fluxo, nem inscreva lead, pelo conector ou pelo navegador.

## Exemplo de prompt

> Analisa o fluxo "Confirmação de inscrição do evento": como ele está performando, onde os leads param e o que eu deveria mexer.

## Exemplo de retorno

Resumo ilustrativo, baseado em uma execução validada em 30/09/2026 (nomes e números alterados):

> # Diagnóstico: Confirmação de inscrição do evento
> **Status:** ativo · **Períodos analisados:** 30 dias (zero), 90 dias (zero) e, por isso, desde a criação
>
> ## Ações presentes (segundo o conector)
> Adicionar Tags, Enviar email. Pelos horários de cada lead, a tag vem segundos antes do email (ordem inferida); o fluxo termina no envio.
>
> ## Números (conector, medido: desde a criação)
> | Etapa | Leads | Diferença (queda ou ramo?) |
> |---|---|---|
> | Entraram (entradas / pessoas) | 120 / 116 | |
> | Adicionar Tags | 119 | 1 entrada |
> | Enviar email | 119 | 0 |
>
> **Emails do fluxo:** 110 entregues de 119 processados; abertura 27% (30/110), clique 10% (11/110, 37% de quem abriu), bounce 7,6% (9/119, todos soft).
>
> ## Achados
> 1. **Ativo sem entrada há 97 dias.** Hipótese: as inscrições do evento acabaram; se a página de inscrição ainda recebe gente, o gatilho quebrou. Como confirmar: aba Entrada no editor.
> 2. **Abertura baixa para uma confirmação:** 27% (n = 110, sinal e não medida firme). Hipótese: aba Promoções, assunto ou remetente. Como confirmar: teste em caixas Gmail e Outlook.
> 3. **Reentrada gerou confirmação em dobro:** 4 pessoas entraram duas vezes. Como confirmar: aba Configurações.
> 4. **Parte das entradas é da equipe ou de teste:** pelo menos 5 das 116 pessoas.
>
> ## Limites e o que não foi visto
> - Não exposto pelo conector: entrada, reentrada, saída e qual tag o fluxo aplica.
> - Não consultado: saídas (limite por hora; num fluxo de duas ações, os números por ação já mostram onde os leads param).
