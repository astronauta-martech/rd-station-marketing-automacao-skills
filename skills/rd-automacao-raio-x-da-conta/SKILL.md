---
name: rd-automacao-raio-x-da-conta
description: "Faz o inventário e a higiene de todos os fluxos de automação de uma conta do RD Station Marketing pelo conector MCP: quantos existem, ativos e inativos, o que fazem os prioritários (ações decodificadas de até 10 fluxos), quais estão parados, esquecidos, duplicados, sem padrão de nome ou com teste ativo, e o que priorizar. Use quando alguém pedir raio-x, auditoria, inventário, diagnóstico geral, limpeza ou visão geral das automações de uma conta, ou perguntar \"quais fluxos temos\" ou \"o que está rodando\". Para um fluxo específico, use rd-automacao-diagnostico-de-fluxo; para lead em vários fluxos ao mesmo tempo, rd-automacao-orquestrar-fluxos. Requer o conector do RD Station Marketing."
license: "Licença de Uso Astronauta Martech 1.0. Pode usar e cobrar por serviços feitos com esta skill; não pode vender a skill. Termos completos em LICENSE."
metadata:
  autor: "Astronauta Martech"
  versao: "0.1.0"
  produto: "RD Station Marketing"
  requer: "conector MCP do RD Station Marketing"
---

# Raio-x dos fluxos da conta

Você levanta todos os fluxos de automação da conta pelo conector e entrega um diagnóstico priorizado. É leitura pura: **nenhuma chamada que escreva na conta**.

## Referências

- `references/conector-rd-marketing.md`: ferramentas, limites e o que o conector não mostra. **Leia primeiro.**
- `references/codigos-de-acao.md`: tradução dos códigos de `workflow_get_details` para o nome da ação no editor.
- `references/estrutura-do-fluxo.md`: regras de desenho usadas nos alertas.
- `references/disciplina-de-evidencia.md`: como escrever números, níveis de certeza e recomendações que mexem em fluxo. **Aplique antes de entregar.**

## Orçamento do conector

Cada ferramenta de escopo "lead" do conector (`workflow_get_details`, `workflow_leads_started_list`, `workflow_leads_at_action_list`, `workflow_leads_exited_list`) tem uma cota própria e pequena (observado, não documentado: cerca de 10 a 20 chamadas a cada meia hora em cada lista de leads e até 10 por hora em `workflow_get_details`; ver `conector-rd-marketing.md`). Nos testes, um 429 numa delas não bloqueou as outras. O raio-x **não mede nem detalha a conta inteira pelo conector**. Com navegador, a listagem da RD dá as contagens de todos os fluxos de uma vez; sem navegador, o raio-x usa só nome, status e datas, e mede uma amostra.

## Passo a passo

Copie e acompanhe:

```
- [ ] 1. Listar todos os fluxos
- [ ] 2. Contagens da listagem (navegador) ou amostra (conector)
- [ ] 3. Classificar e aplicar os alertas sem detalhe
- [ ] 4. Detalhar até 10 fluxos prioritários
- [ ] 5. Entregar o relatório
```

1. **Listar:** `workflows_search` com `page_size` "100", paginando até vir vazio. Guarde id, nome, status, criador, `created_at`, `updated_at`.
2. **Contagens:**
   - **com navegador:** abra Automação de Marketing e leia a tabela de todas as páginas (50 por página): **Entrada de leads** (total) e **Leads ativos** (no instante da leitura; anote a hora). Não clique em nada além da paginação: o menu de cada fluxo tem Desativar, Excluir fluxo e Inserir Leads no fluxo.
   - **sem navegador:** `workflow_leads_started_list` dos últimos 30 dias em **até 10 fluxos ativos**, nesta ordem: possíveis duplicados, teste no nome, CRM e integração no nome, editados mais recentemente. Use `page_size` 1 **só como teste de presença**: `total_count` 1 quer dizer "teve entrada em 30 dias", 0 quer dizer "nenhuma" (o campo é o tamanho da página, não o total; não é contagem). A resposta traz um lead com nome e email: não repita no relatório. Para quem der 0 em 30 dias, uma segunda chamada com `start_date` na data de criação separa "Ativo sem entrada" de "Ativo parado"; ela conta no mesmo teto de 10. Se vier 429, aplique a seção 6 de `disciplina-de-evidencia.md`; se persistir, pare essa ferramenta e liste os não medidos como "não medido (limite do conector)", nunca como "sem entrada".
3. **Classificar** com nome, status, datas e contagens, e aplicar os alertas que não precisam de detalhe.
4. **Detalhar** com `workflow_get_details` até 10 fluxos, nesta prioridade: ativos com teste no nome; até 3 ativos com CRM, Oportunidade, Negociação ou Venda no nome (para os alertas que precisam de detalhe); ativos com leads ativos na leitura; possíveis duplicados (**sempre em par**; se sobrar uma vaga só, passe ao próximo grupo); ativos esquecidos com entrada. Traduza os códigos com `codigos-de-acao.md`. Se vier 429, aplique a seção 6 de `disciplina-de-evidencia.md`; se persistir, pare de detalhar e siga com o que tem (as medições do passo 2 continuam valendo: as cotas parecem separadas por ferramenta).
5. **Reler e entregar:** uma última `workflows_search` (fora do orçamento de lead) pega fluxos ativados ou desativados durante a análise. Entregue no formato de saída, dizendo de onde veio cada contagem (listagem ou conector) e a cobertura (medidos de total, detalhados de total).

## Alertas

| Alerta | Como detectar | Precisa de detalhe? | Por que importa |
|---|---|---|---|
| Ativo sem entrada | Ativo e 0 entradas no total: pela listagem, ou pelo conector com `start_date` na data de criação | não | Gatilho quebrado, lista vazia ou fluxo morto. |
| Ativo parado | Ativo, total de entradas > 0 (listagem, ou conector desde a criação) e **0 entradas medidas em 30 dias** (conector) | não | Campanha que acabou e ficou ligada. Com a listagem sozinha não dá para saber (fluxo sem espera sempre mostra 0 leads ativos): não aplique. |
| Sem entrada em 30 dias | Ativo, 0 entradas em 30 dias (conector) e total não medido | não | Pode ser "sem entrada" ou "parado": diga que falta a medição desde a criação. |
| Teste ativo | Ativo e nome com teste, temp, rascunho, exemplo, lab, laboratório, sandbox, "não ativar" | não | Pode disparar para lead real. |
| Evento vencido | Ativo com data, ano ou edição passada no nome (ex.: "Webinar 2024", "Black Friday 2025", "2025-01-15") | não | Campanha que acabou e ficou ligada. |
| Esquecido | Ativo, sem edição há mais de 12 meses, com entrada **e que envia algo ao lead** (pelo detalhe ou pelo nome) | não | Conteúdo, datas e ofertas podem estar vencidos. Fluxos só de tag ou integração entram na contagem, não na lista. |
| Duplicado? | Dois ou mais ativos com nomes idênticos depois de tirar rótulos entre colchetes, espaços e sufixos ("- Fluxo 2", "Atualizado", "v2", "V 2.0", "cópia"), ou irmãos com o mesmo total de entradas | não | Lead pode receber duas vezes. Sem "?" só quando entrada e destino foram comparados. |
| Fora do padrão de nome | Sem o padrão dominante da conta | não | Dificulta achar e auditar. |
| Arquivável | Inativo e sem alteração há mais de 12 meses (use `updated_at` como aproximação; não escreva "desativado em" nem "nunca ativado") | não | Limpeza da listagem. |
| Oportunidade sem responsável? | Tem `MKOPP` e nenhum `OWROU` | sim | Lead pode chegar ao CRM sem dono (regra 3). Alterar responsável pelos Leads ainda não tem código: com código não mapeado no fluxo, escreva "conferir no editor". O conector não mostra a ordem nem se o lead já tinha dono. |
| CRM sem negociação? | Tem ação de CRM (`CRMUT`, ou código não mapeado em fluxo com CRM, Negociação ou Venda no nome: a maioria das ações de CRM ainda não tem código) e nenhum `CRMOP` | sim | Pode não estar fazendo nada (regra 4). `CRMOP` é mapeamento provável e a negociação pode vir de outro fluxo: escreva como hipótese a conferir no editor. |

**Ordem de gravidade para as Prioridades:** 1) Teste ativo; 2) Duplicado? ou Evento vencido em fluxo que envia algo ao lead; 3) Oportunidade sem responsável? e CRM sem negociação?; 4) Ativo sem entrada, Ativo parado e Sem entrada em 30 dias; 5) Esquecido; 6) Arquivável e padrão de nome. A tabela "Ativos com alerta" mostra os níveis 1 a 4. Esquecido e Fora do padrão de nome entram em "Outros alertas", com a contagem e a lista completa de cada um (seção 7 de `disciplina-de-evidencia.md`); Arquivável fica em "Inativos arquiváveis".

Detecte o padrão de nome dominante olhando os nomes, não imponha um. Se um segundo padrão cobrir pelo menos 10% dos nomes, trate os dois como aceitos e diga isso com a contagem de cada um. `#<número>` pode ser número de tarefa ou data no formato AAMMDD (confira contra a data de criação). Aponte também variações de rótulo (ex.: `[TAG]`, `[+Tag]`, `[+tag]`), erros de digitação e espaços sobrando. Não tire conclusão de **ordem** das ações: o conector não devolve a ordem do percurso.

## Formato de saída

```markdown
# Raio-x dos fluxos: <conta> · <data>

## Cobertura
- <N> fluxos: <A> ativos, <I> inativos (releitura final às HH:MM)
- Contagens: <listagem do navegador, todos, lida às HH:MM | conector, <m> de <A> ativos medidos (DD/MM a DD/MM)>
- Detalhados: <d> de <A> (<teto de 10 da skill | parou no 429 após <n> chamadas>)

## Resumo
- Entradas: <...> (<fonte>) · Leads ativos no instante da leitura (HH:MM): <...> ("-" lido como 0; só com a listagem)
- Padrão de nome dominante: <padrão> (<x> de <N> nomes)

## Prioridades
1. **<alerta mais grave>:** <fluxos>. O que fazer: <...>
2. ...

## Fluxos por situação
### Ativos com alerta
| Fluxo | Entradas | Leads ativos | Última edição | Alertas |
### Outros alertas (níveis 5 e 6)
- Esquecido: <n> (<lista completa>)
- Fora do padrão de nome: <n> (<lista completa>)
### Ativos sem alerta
- <n> fluxos (<lista>)
### Inativos arquiváveis
- <n> fluxos (<lista>)

## O que assumi
- <"-" lido como 0; rótulos de evento lidos como data passada; padrões de nome aceitos>

## Limites e o que não foi visto
- Não exposto pelo conector: entrada, configurações, saída e a configuração das ações.
- Não consultado: <o que ficou sem medir ou detalhar, com o motivo e o próximo lote sugerido>
```

Datas em DD/MM/AAAA. Sem nome ou email de lead no relatório.

## Limites

- Os alertas são indícios, não prova: "ativo sem entrada" pode ser um fluxo sazonal. Escreva como hipótese a confirmar.
- Nunca ative, desative, edite, duplique ou exclua fluxo, nem inscreva lead, pelo conector ou pelo navegador. Recomende a ação; quem executa é a pessoa.

## Exemplo de prompt

> Faz um raio-x dos fluxos de automação da conta: o que está ativo, o que está esquecido e o que eu arrumo primeiro.

## Exemplo de retorno

Resumo ilustrativo, baseado em uma execução validada em 30/09/2026 (nomes e números alterados):

> # Raio-x dos fluxos: <conta> · 30/09/2026
> ## Cobertura
> - Todos os fluxos, ativos e inativos; nenhuma mudança na releitura final
> - Contagens: listagem do navegador, todos, lida às 12:24
> - Detalhados: 10 dos ativos (teto de 10 da skill; nenhum 429)
>
> ## Resumo
> - A conta roda no piloto automático: só 3 ativos editados nos últimos 6 meses, e 5 fluxos somam 80% das entradas dos ativos (listagem). Nenhum teste ativo: os fluxos com cara de teste estão inativos.
>
> ## Prioridades
> 1. **Duplicado?:** "Cadastro na plataforma A" e "Cadastro na plataforma A - Fluxo 2", os dois ativos, cada um com uma única ação (Enviar Leads para Integração, mesmo tipo de ação) e o mesmo total de entradas; a cópia nasceu menos de uma hora depois da última edição do original. Hipótese: cada lead vai duas vezes para a integração. O que fazer: comparar entrada e integração no editor e, se forem iguais, desativar um deles. Confirme antes de desativar.
> 2. **Evento vencido em fluxo que envia ao lead:** vários ativos com data ou edição passada no nome, entre eles o de um webinar do ano passado com Enviar email e `SDSMS` (provável Enviar SMS). O que fazer: ver se a entrada ainda recebe lead; se receber, desativar; o que for perene, renomear sem a data.
> 3. **Integrações de alto volume sem edição há mais de 12 meses** (pelo nome, não detalhadas): se a conexão do outro lado caiu, o fluxo segue "funcionando" na RD e nada acontece fora. O que fazer: testar o destino de cada uma.
>
> ## Limites e o que não foi visto
> - Não exposto pelo conector: entrada, configurações, saída e configuração das ações.
> - Não consultado: alertas de CRM (nenhum fluxo de CRM entrou nos 10 detalhes). Próximo lote sugerido: 10 fluxos, começando pelos de CRM.
