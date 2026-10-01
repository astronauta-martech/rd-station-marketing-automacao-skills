---
name: rd-automacao-orquestrar-fluxos
description: "Mapeia como os fluxos de automação de uma conta do RD Station Marketing se relacionam e sugere como reduzir o risco de o mesmo lead receber comunicações concorrentes: encontra leads que entraram em dois ou mais fluxos ativos no mesmo período, fluxos que adicionam ou removem leads de outros fluxos, cadeias por nome e sobreposição de públicos, e propõe a orquestração (remover de outros fluxos, saída por conversão, unir caminhos, prioridade). Use quando alguém falar em lead recebendo emails demais, fluxos brigando, sobreposição, conflito, prioridade entre automações, encadear fluxos ou mapa de automações. Requer o conector do RD Station Marketing."
license: "Licença de Uso Astronauta Martech 1.0. Pode usar e cobrar por serviços feitos com esta skill; não pode vender a skill. Termos completos em LICENSE."
metadata:
  autor: "Astronauta Martech"
  versao: "0.1.0"
  produto: "RD Station Marketing"
  requer: "conector MCP do RD Station Marketing"
---

# Orquestração entre fluxos

Você mede quantos leads entraram em mais de um fluxo ativo no mesmo período e propõe regras para que cada lead esteja em um fluxo por vez. Leitura pura: **nenhuma chamada que escreva na conta**.

## Referências

- `references/conector-rd-marketing.md`: ferramentas e limites. **Leia primeiro.**
- `references/codigos-de-acao.md`: códigos de **Adicionar Leads a outros fluxos**, **Remover Lead de outros fluxos** (ainda sem código) e demais. Leia no passo 5.
- `references/estrutura-do-fluxo.md`: regras 6, 7, 12, 14 e 15 (encadeamento, remoção, saída, divisões encadeadas, editar fluxo ativo e dois fluxos com a mesma entrada). Leia no passo 6.
- `references/catalogo-acoes.md`: o que a RD avisa sobre adicionar e remover de outros fluxos.
- `references/disciplina-de-evidencia.md`: como escrever números, níveis de certeza e recomendações que mexem em fluxo. **Aplique antes de entregar.**

## Passo a passo

```
- [ ] 1. Listar os fluxos (e ler a listagem, se houver navegador)
- [ ] 2. Escolher até 10 fluxos para medir
- [ ] 3. Medir sobreposição de leads
- [ ] 4. Achar famílias por nome
- [ ] 5. Detalhar até 5 fluxos com sobreposição
- [ ] 6. Desenhar a orquestração
```

1. **Fluxos:** `workflows_search` com `page_size` "100", paginando até vir vazio. A ferramenta não filtra por status: separe localmente os de `status` `enabled` e guarde a lista completa, ativos e inativos, para o passo 4. Com navegador, leia também a listagem da RD (Entrada de leads e **Leads ativos** de todos os fluxos, 50 por página, anotando a hora). No navegador, não clique em nada além da paginação: o menu de cada fluxo tem Desativar, Excluir fluxo e Inserir Leads no fluxo.
2. **Triagem sem cota e escolha de onde medir:**
   - Com a listagem, **Leads ativos** mostra quem está percorrendo **no instante da leitura (HH:MM)**: é um retrato, não prova de ausência de conflito (fluxo de passagem imediata sempre mostra 0 e ainda pode colidir). O teto de leads em comum nesse instante num par é o menor dos dois valores (calculado). Calcule o teto para **todos** os pares de ativos e informe no Mapa quantos têm teto maior que zero.
   - Sinal forte e gratuito de duplicado ou cadeia: fluxos irmãos pelo nome com o **mesmo total** de Entrada de leads. Antes de chamar de duplicado, compare a data de criação do irmão com as primeiras entradas do outro: o total pode bater por coincidência.
   - Escolha **até 10 fluxos**, nesta ordem:
     1. os que a pessoa citou ou que são do tema pedido;
     2. os com leads ativos na leitura;
     3. os que, pelo nome, enviam algo ao lead. Palavras de exemplo (lista aberta): email, SMS, WhatsApp, jornada, nutrição, boas-vindas, newsletter, autorresponder, confirmação, convite, envio, onboarding. Ignore nomes em que a palavra descreve um critério (ex.: "Tag para quem usa email corporativo"). Aplique o critério a **todos** os ativos por script e ordene do maior total de Entrada de leads para o menor;
     4. pares com o mesmo total (um par ocupa duas vagas; se sobrar uma, passe ao próximo grupo).

     Pule fluxos com até 2 entradas desde a criação. Sem navegador, mantenha a ordem acima sem os critérios que dependem da listagem (leads ativos, total de entradas, até 2 entradas) e desempate pelos editados mais recentemente. Diga quantos ficaram de fora.
   - Antes de planejar a medição, teste a cota **já medindo** o menor fluxo escolhido (`page_size` 100): custa o mesmo que uma sonda e o resultado já é dado. Se voltar 429, aplique a seção 6 de `disciplina-de-evidencia.md` (espere o `cooldown_seconds` devolvido, se for até 120 segundos, e repita uma vez). Se persistir, entregue em **modo sem medição**: com navegador, os tetos e sinais da listagem; sem navegador, só famílias por nome e o detalhe do passo 5 (que tem cota própria). Mesmo formato, com a tabela de Sobreposições como "Pares a medir" e o plano de medição pronto em "Limites e o que não foi visto".
   - Meça primeiro o fluxo mais importante: o limite pode vir em qualquer chamada.
3. **Sobreposição de leads (o núcleo):** para os escolhidos, `workflow_leads_started_list` no período, `page_size` 100. O período precisa cobrir a espera mais longa dos fluxos envolvidos (quem está percorrendo agora pode ter entrado meses atrás): padrão 30 dias; para fluxos com esperas longas ou datas fixas, 90 dias ou desde a criação. O conector não mostra a duração das esperas: trate como espera longa quando Leads ativos for uma fração grande da Entrada de leads. O custo se estima pela listagem (entradas ÷ 100 = páginas; a Entrada de leads conta desde a criação, então um período curto custa menos). Some as páginas dos escolhidos antes de começar. Se passar de 15 chamadas (a cota observada de `workflow_leads_started_list` é de cerca de 10 a 20 por meia hora), corte fluxos ou período até caber e diga o que ficou de fora. O `total_count` é o tamanho da página, não o total: se uma página vier com 100 registros, divida o período em blocos de data com menos de 100 entradas (a paginação sem ordem repete e pula registros) e confira a soma com a listagem. Guarde os horários sem truncar. Cruze por `contact_id`: para cada par, quantos leads **entraram nos dois no período** (dados até o dia anterior). "Nos dois ao mesmo tempo" exige as saídas **dos dois fluxos do par** (`workflow_leads_exited_list`, ao menos uma chamada por fluxo e mais uma por página extra, com limite de 1 a 12 por hora conforme o plano: na prática, só para o par mais importante) ou comparar datas de entrada com a duração conhecida do fluxo; sem isso, escreva "entraram nos dois". Mostre só contagens. Para 429, seção 6 de `disciplina-de-evidencia.md`.
4. **Famílias por nome:** aplique o critério a **todos os fluxos da conta, ativos e inativos** (a lista completa do passo 1), e liste todas as famílias que batem (ou "entre outras", com o total). Agrupe nomes que indicam cadeia ou família, inclusive os que repetem o mesmo número de tarefa ou demanda (`#NNNN`). Exemplos: `[Fluxo] Abriu: X` e `[Fluxo] Clicou: X`; mesmo prefixo de campanha; "Fluxo 2", "Atualizado".
5. **Detalhe só onde há sobreposição:** `workflow_get_details` em **até 5 fluxos** dos pares com mais leads em comum (se nada foi medido, dos pares escolhidos pelos sinais da triagem, marcando isso), para saber se enviam algo ao lead (`SDEMA`, `SDSMS`; código não mapeado pode ser WhatsApp) e se adicionam leads a outros fluxos (`ADLTW`). O orçamento dessa ferramenta é de poucas chamadas por hora; para 429, seção 6 de `disciplina-de-evidencia.md`. **Plano B** se o detalhe ficar bloqueado: uma chamada de `emails_by_workflow_analytics` com os `workflow_id` escolhidos, no período medido. Envios registrados provam que envia; lista vazia só quer dizer "sem registro no período" e, sozinha, não prova que não envia. Até o detalhe ou o plano B responderem, a coluna "Os dois enviam ao lead?" fica "não detalhado", sem palpite pelo nome. Sequências de códigos iguais entre dois fluxos são sinal de "mesmo desenho (provável cópia)", não de "mesma ação". O conector **não diz qual é o fluxo de destino** de `ADLTW`: liste para conferência no editor. **Remover Lead de outros fluxos** ainda não tem código mapeado: qualquer código fora da tabela pode ser ele; liste como "(código não mapeado)" para conferir.
6. **Desenhe a orquestração** para os pares com sobreposição relevante:
   - **Prioridade:** qual fluxo manda quando o lead está nos dois (em geral, o mais próximo da compra).
   - **Remover Lead de outros fluxos** no início do fluxo prioritário, apontando os concorrentes um a um. Segundo a documentação da skill (que reproduz o texto da RD), a remoção vale até o fim do caminho atual; o efeito não foi testado com lead. Não recomende a opção "remover de todos os outros fluxos de automação" sem a pessoa confirmar a lista de fluxos afetados: ela atinge fluxos de outras equipes.
   - **Saída por conversão ou por marcação de Oportunidade** no fluxo de nutrição, para quem avançou para o comercial (a saída não reconhece entrada em outro fluxo, segmentação nem tag).
   - **Adicionar Leads a outros fluxos** no fim de um fluxo para passar o bastão, lembrando que ignora a entrada do destino, exige destino ativo e faz o lead percorrer de novo.
   - **Unificar** fluxos duplicados em vez de orquestrar os dois. Cada Dividir caminho só tem SIM e NÃO: unificar três fluxos exige divisões encadeadas (regra 14). Editar fluxo ativo vale só daqui para frente, e dois ativos com a mesma entrada duplicam tudo (regra 15): decida entre editar o atual ou criar outro e desativar o antigo.

   **Cada regra, e cada par dentro de uma regra**, cumpre a seção 5 de `disciplina-de-evidencia.md`: conferências no editor, leads em andamento, qual manter e por quê, impacto se desligar o errado, "confirme antes". Unificar ou substituir exige transição por fluxo que sai: bloquear novas entradas, deixar quem está terminar, desativar só com Leads ativos em zero. Regra para fluxo não detalhado é condicional.

## Formato de saída

```markdown
# Orquestração de fluxos: <conta> · <data>

## Mapa
- <N> fluxos ativos; <L> com leads percorrendo na leitura das HH:MM (listagem); <P> de <total> pares com teto maior que zero (calculado); <K> famílias por nome.
- Modo: <medição completa | sem medição (cota bloqueada às HH:MM), com listagem | sem medição, sem listagem>
- Medidos: <m> fluxos (medido: DD/MM a DD/MM); detalhados: <d> de <N> (<teto da skill | 429>).
- <M> de <d> detalhados com ações entre fluxos.

## Sobreposições
| Fluxo A | Fluxo B | Entraram nos dois (medido: DD/MM a DD/MM) | Teto na leitura das HH:MM (calculado: menor Leads ativos) | Sinal | Os dois enviam ao lead? |
|---|---|---|---|---|---|
(pares só com sinal de nome ou total igual entram com "não medido"; no modo sem medição, a tabela se chama "Pares a medir")

## Regras propostas
1. <regra>. Base: <o que foi observado>. Confira antes: <...>. Leads em andamento: <...>. Confirme antes de aplicar.

## A conferir no editor
- <destinos de adicionar/remover, entradas, saídas>

## O que assumi
- <período, critérios de escolha dos fluxos, "-" lido como 0, interpretações de nome>

## Limites e o que não foi visto
- Não exposto pelo conector: entrada, saída, reentrada, configuração das ações, ramos e fluxo de destino de "Adicionar Leads a outros fluxos".
- Não consultado: <fluxos e pares sem medir ou detalhar, com o motivo; no modo sem medição, o plano de medição pronto>
```

## Limites

- Sobreposição de entrada no mesmo período não prova envio simultâneo; é indício forte quando os dois fluxos enviam email. Escreva como risco.
- Dados de leads por fluxo são diários.
- Nunca ative, desative, edite, duplique ou exclua fluxo, nem inscreva lead, pelo conector ou pelo navegador. As regras são recomendação para a pessoa aplicar no editor. `rd-automacao-montar` só cria fluxo novo e inativo a partir de uma especificação: use-a quando a regra for um fluxo novo (ex.: unificar dois fluxos num terceiro, especificado com `rd-automacao-briefing`), nunca para editar fluxo ativo (regra 15).

## Exemplo de prompt

> Tem lead reclamando que recebe email demais. Vê quais fluxos ativos disputam o mesmo lead e propõe a prioridade.

## Exemplo de retorno

Resumo ilustrativo, baseado em uma execução validada em 30/09/2026 (nomes e números alterados):

> # Orquestração de fluxos: <conta> · 30/09/2026
> ## Mapa
> - Só 3 fluxos ativos com leads percorrendo na leitura das 12:39 (listagem), todos da família "Aniversário"; só 3 pares com teto maior que zero (calculado), todos nessa família.
> - Modo: sem medição (cota bloqueada às 12:42), com listagem. `workflow_leads_started_list` e `workflow_get_details` devolveram 429 duas vezes cada (cooldown devolvido de 60 s).
> - Medidos: 0 de 10 escolhidos; detalhados: 0.
>
> ## Pares a medir
> | Fluxo A | Fluxo B | Entraram nos dois | Teto na leitura das 12:39 (calculado) | Sinal | Os dois enviam ao lead? |
> |---|---|---|---|---|---|
> | Aniversário A | Aniversário B | não medido (429) | 23 | família por nome; os dois com leads percorrendo | não detalhado; pelo nome, sim |
> | Cadastro na plataforma A | Cadastro na plataforma A - Fluxo 2 | não medido | 0 | mesmo total de entradas; a cópia nasceu menos de uma hora depois da última edição do original | não detalhado; pelo nome, integração |
>
> ## Regras propostas (todas condicionais: nada foi medido nem detalhado)
> 1. **Aniversário: um dono por lead.** Se, no editor, os três enviam mensagem ao lead e as entradas podem pegar a mesma pessoa: **Remover Lead de outros fluxos** no início do prioritário, apontando os outros dois (segundo a documentação da skill, vale até o fim do caminho atual), ou unificar com **Dividir caminho por segmentação** encadeado. Prioridade para o de maior base; se a mensagem for específica de cada produto, quem usa mais os outros passa a receber a do prioritário. Não desative os outros para resolver: quem está percorrendo sai. Confirme antes de aplicar.
> 2. **"Cadastro na plataforma A" e a cópia: duplicado ou cadeia?** Mesma entrada no editor: duplicado provável, manter o que chama a integração certa. Cópia com "Definir entrada depois" e o original terminando em Adicionar Leads a outros fluxos: cadeia de propósito, manter os dois. Confirme antes de desativar.
>
> ## Limites e o que não foi visto
> - Não consultado (limite do conector): interseção de leads e códigos de ação. Plano pronto: 12 chamadas, começando pelos três de Aniversário desde a criação.
