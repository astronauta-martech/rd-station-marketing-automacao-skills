# Conector MCP do RD Station Marketing: o que ele enxerga dos fluxos

Referência das ferramentas do conector oficial do RD Station Marketing (MCP) usadas pelas skills que leem ou testam a conta. Os nomes abaixo são os nomes das ferramentas no conector; no seu cliente eles podem aparecer com um prefixo (ex.: `mcp__<id>__workflows_search`).

Só vale o que o conector expõe. Se uma informação não vem por ele, a skill diz que não tem acesso a ela, em vez de supor.

## Ferramentas de fluxo

| Ferramenta | Entrada | Devolve | Observações |
|---|---|---|---|
| `workflows_search` | `search` (nome), `page`, `page_size`, `order`, `ids` | id, nome, quem criou/editou, datas, `status` (`enabled`/`disabled`) | Porta de entrada. `page_size` "100" traz a conta pequena de uma vez. |
| `workflow_get_details` | `id` | nome, status, datas e `actions`: lista de `{id, type}` | **Só o código de cada ação**, sem configuração, sem entrada, sem ramos. Ver "Códigos de ação". |
| `workflow_leads_started_list` | `id`, `start_date`, `end_date`, `page`, `page_size` (padrão 25), `order` | quem entrou e quando, e `total_count` | **Atualiza uma vez por dia.** Não serve para decisão em tempo real. **`total_count` é o número de registros da página devolvida, não o total do fluxo** (um fluxo com mais de 100 entradas devolveu `total_count` 100 com `page_size` 100). Com `page_size` 1, ele só responde "teve entrada no período?" (0 ou 1). Ver "Contar entradas" abaixo. |
| `workflow_leads_at_action_list` | `id`, `action_id`, datas, paginação | quem chegou numa ação e quando | Diário. O `action_id` vem de `workflow_get_details`. |
| `workflow_leads_exited_list` | `id`, datas, paginação | quem saiu e quando | Diário e com **limite por hora**: 1 (Light/Basic), 3 (Pro), 12 (Advanced). Use com parcimônia. |
| `emails_by_workflow_analytics` | `start_date`, `end_date`, `workflow_id` (lista) | métricas dos emails do fluxo: entregues, abertos, clicados, bounces | **Plano Pro ou superior.** |
| `workflow_lead_enroll` | `id`, `leads` (emails ou UUIDs) | confirmação | **Escreve.** Dispara o fluxo na hora: emails saem, estágios mudam, notificações disparam. Só funciona em fluxo **ativo**. Limite por chamada: 10 (Light/Basic), 25 (Pro), 50 (Advanced). |

## Ferramentas de apoio

| Ferramenta | Uso nas skills |
|---|---|
| `segmentation_list` | Achar o ID de uma segmentação (listas de teste, compradores, presentes). |
| `segmentation_contacts_list` | Contatos de uma segmentação (nome e email). Traz dado pessoal: usar para validação, nunca para exportar. |
| `get_contact_by_id_email` | Dados de um contato, inclusive tags, para conferir o efeito de um teste. |
| `contact_funnel_stage_get` | Estágio do funil, marcação de oportunidade e dono do contato. |
| `get_contact_events` | Conversões (`CONVERSION`) ou oportunidades (`OPPORTUNITY`) de um contato, pelo UUID. |
| `emails_search` | Buscar emails por nome; `types: ["WORKFLOW_EMAIL"]` lista os emails de automação. |
| `email_get_by_id` | Metadados de um email (assunto, remetente, status), sem o HTML. |

## O que o conector não mostra

- A **entrada** do fluxo (gatilho), as **configurações** (reentrada, finais de semana) e a **saída**.
- A **configuração** de cada ação (qual email, qual tag, qual funil) e a **estrutura de ramos** (SIM/NÃO).
- Criar, editar, ativar ou desativar fluxo.

Para qualquer um desses pontos é preciso olhar a tela do editor (pessoa ou agente no navegador). Uma skill que só usa o conector deve dizer isso no resultado.

## Códigos de ação

`workflow_get_details` devolve cada ação como um código de 5 letras. Tradução e cuidados de leitura em `codigos-de-acao.md`.

## Contar entradas de um fluxo

- **Com navegador, prefira a listagem:** a coluna Entrada de leads já é o total desde a criação, sem gastar cota.
- **Pelo conector:** `page_size` efetivo máximo é 100. A paginação sem ordem **repete e pula registros** entre páginas, e o parâmetro `order` não resolveu isso. Divida o período em **blocos por data** que tenham menos de 100 entradas cada, peça uma página por bloco e some. Confira a soma com a Entrada de leads da listagem quando houver.
- **Custo:** um bloco por chamada. Estime pelos totais da listagem antes de começar e corte o período se passar da cota.
- **`contact_id` nulo:** algumas entradas vêm sem `contact_id`. Elas contam como entrada, mas ficam fora de cruzamentos entre fluxos; declare quantas foram.
- **Horários:** guarde a data e hora exatamente como vieram (com segundos e fração), sem truncar: é o que permite dizer quem entrou primeiro.
- **Timeout** (erro sem `cooldown_seconds`, não repetível): não repita o mesmo pedido; reduza o período ou passe para outro fluxo. Chamadas com erro também parecem consumir a cota.

## Achar conversões, campos e dependências

| Ferramenta | Uso |
|---|---|
| `landing_pages_search` | Landing pages e o **identificador de conversão** de cada uma (entrada e saída de fluxos). |
| `forms_search` | Formulários e seus identificadores de conversão. |
| `contact_custom_fields_list` | Campos personalizados da conta (útil para anotação e nome de negociação). A resposta pode ser grande. |

Conversões que chegam de fora dos formulários e landing pages da RD (site próprio, integrações) não aparecem nessas buscas: se não achar, pergunte o identificador à pessoa.

## Comportamentos observados

- **Limite real de `workflow_get_details`:** a descrição fala em 120 por minuto, mas na prática a ferramenta aceitou **cerca de 14 chamadas** e depois devolveu **429** (`cooldown_seconds: 60`, escopo "lead") por **mais de 30 minutos**, mesmo com esperas de 1 a 10 minutos entre tentativas. As outras ferramentas continuaram funcionando. Trate como um orçamento de **até 10 detalhes por hora** (observado; a RD não documenta o limite real): escolha os fluxos que importam, nunca detalhe a conta inteira, e diga quantos ficaram sem detalhe.
- **`workflow_get_details` devolve só id, nome, status, datas e a lista de ações** (código + id). Não traz gatilho, configuração nem ramos, e **a ordem das ações não é a ordem do fluxo** (ver `codigos-de-acao.md`).
- **As listas de leads por fluxo também têm limite apertado.** Numa varredura, `workflow_leads_started_list` aceitou cerca de 20 chamadas e bloqueou (429, escopo "lead") por cerca de 35 minutos. As cotas parecem **separadas por ferramenta** (numa execução, `workflow_leads_started_list` estava bloqueada enquanto `workflow_get_details` respondeu 5 de 5). Trate cada lista de leads (`workflow_leads_started_list`, `workflow_leads_at_action_list`, `workflow_leads_exited_list`) como tendo **cerca de 10 a 20 chamadas por meia hora**, e `workflow_get_details` como tendo **até 10 por hora** (item acima). São números observados, não documentados. Nunca varra a conta inteira por elas. Regra de 429 em `disciplina-de-evidencia.md`.
- **A listagem da RD atualiza a coluna "Entrada de leads" mais rápido** que o conector (que atualiza por dia).
- **`contact_funnel_stage_get`** devolve estágio, oportunidade e dono do contato; `get_contact_by_id_email` devolve tags mas não esses campos.
- **`segmentation_list`** devolveu só 25 segmentações numa conta que tem mais. Não conclua que uma lista "não existe" só por ela não aparecer; marque "conferir".
- **`emails_by_workflow_analytics`** separa as métricas por ação de envio: o mesmo email em duas ações aparece duas vezes. Pode dar timeout; tente uma vez de novo.
- **Amostra pequena engana:** com poucos envios, 100% de abertura não quer dizer nada. Informe sempre o número de contatos (n) e amplie o período quando n for pequeno.

## Alternativa sem cota: a listagem da RD no navegador

Com um navegador conectado, a tela **Automação de Marketing** mostra, para todos os fluxos, 50 por página: nome, status, **Entrada de leads** (total desde a criação), **Leads ativos** (percorrendo agora; "-" é zero), criação e última alteração. Uma leitura da tabela (texto da página ou árvore de acessibilidade) substitui dezenas de chamadas ao conector. Use-a para inventário e triagem; guarde o conector para o detalhe de poucos fluxos.

Dicas de leitura: o texto da página (`get_page_text`) pode vir vazio nessa tela; ler as linhas da tabela por script (`innerText` de cada linha) funciona, em blocos se a saída for truncada. Depois de trocar de página, a tabela fica alguns segundos com linhas vazias: espere carregar antes de ler. Clicar no nome de um fluxo pode abrir outra aba.

## Boas práticas de uso

1. **Liste antes de detalhar:** `workflows_search` uma vez, depois `workflow_get_details` só nos fluxos que importam.
2. **Respeite os limites reais, não os anunciados:** a descrição do conector fala em 120 chamadas por minuto por conta, mas as ferramentas de escopo "lead" bloquearam bem antes nos testes (ver Comportamentos observados). `workflow_leads_exited_list` ainda tem limite por hora.
3. **Datas no formato `AAAA-MM-DD`.** O filtro de leads começa no máximo na data de criação do fluxo.
4. **Dado de lead é pessoal.** Em relatório, mostre contagens; nomes e emails só quando a pessoa pedir e precisar.
5. **Nunca chame `workflow_lead_enroll` sem confirmação explícita** de qual fluxo e qual contato, e sem conferir que o fluxo está ativo e o que ele dispara.
