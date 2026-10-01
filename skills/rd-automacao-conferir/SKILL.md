---
name: rd-automacao-conferir
description: "Confere um fluxo de automação já montado no RD Station Marketing contra a especificação ou o objetivo dele, antes de ativar: lê o editor pelo navegador (entrada, blocos, configurações, saída) e o conector MCP (tipos de ação e status), compara item a item e aplica as regras de desenho. Use quando alguém pedir para conferir, validar, fazer QA ou checar se um fluxo recém-montado ou inativo está pronto para ativar. Para fluxo já rodando (performance, onde os leads param), use rd-automacao-diagnostico-de-fluxo. Entrega lista de divergências e riscos com a correção de cada um; não altera o fluxo."
license: "Licença de Uso Astronauta Martech 1.0. Pode usar e cobrar por serviços feitos com esta skill; não pode vender a skill. Termos completos em LICENSE."
metadata:
  autor: "Astronauta Martech"
  versao: "0.1.0"
  produto: "RD Station Marketing"
  requer: "navegador com sessão do RD Station Marketing e/ou conector MCP do RD Station Marketing"
---

# Conferir fluxo antes de ativar

Você compara o que está montado com o que deveria estar e aponta cada divergência com a correção. **Você não edita o fluxo.**

## Referências

- `references/estrutura-do-fluxo.md`: as 15 regras de desenho (checklist obrigatório) e a anatomia do editor.
- `references/formato-especificacao.md`: como ler a especificação de referência.
- `references/catalogo-acoes.md`: campos e cuidados de cada ação.
- `references/conector-rd-marketing.md` e `references/codigos-de-acao.md`: leitura pelo conector.
- `references/disciplina-de-evidencia.md`: como escrever números, níveis de certeza e recomendações que mexem em fluxo. **Aplique antes de entregar.**

## Fontes de verdade, em ordem

1. **Tela do editor** (navegador): única fonte de entrada, configuração de cada bloco, ramos, reentrada, finais de semana e saída.
2. **Conector** (`workflow_get_details`): confirma status e o conjunto de códigos de ação (a ordem devolvida não é a do percurso e não há ramos). Serve para checar se a contagem por tipo bate com a tela.
3. **Especificação** ou, na falta dela, o objetivo descrito pela pessoa.

Sem navegador, a conferência fica restrita ao conector: diga isso no início do resultado e liste o que não pôde ser conferido.

## Passo a passo

```
- [ ] 1. Localizar o fluxo e a referência
- [ ] 2. Ler a tela: entrada, blocos, ramos
- [ ] 3. Ler Configurações e Saída
- [ ] 4. Ler o conector e cruzar com a tela
- [ ] 5. Comparar com a especificação
- [ ] 6. Aplicar as 15 regras
- [ ] 7. Relatório
```

1. **Localizar:** pelo nome (`workflows_search` e/ou a listagem em Automação de Marketing). O id do conector é o mesmo da URL do editor (`.../editor/<id>`). Abra no editor **sem clicar em Salvar**. Clicar no nome na listagem pode abrir outra aba.
2. **Tela:** para cada bloco, registre ação e texto visível (o bloco mostra o resumo da configuração). Resumos longos aparecem truncados no cartão (ex.: tag "campanha-maio-recupera…"): leia o texto completo pela árvore de acessibilidade ou pelo tooltip antes de abrir o painel. Abra o painel de um bloco só se o resumo não bastar, e feche no X sem alterar.
3. **Configurações e Saída:** abrem como painéis laterais a partir dos links no topo; registre reentrada, finais de semana e condições marcadas, e feche no X.
4. **Conector:** `workflow_get_details`; traduza os códigos e compare a contagem por tipo com a tela, nunca a ordem. Códigos fora da tabela e ações sem código observado (Unir caminho, Dividir caminho por email do fluxo e as demais listadas em `codigos-de-acao.md`) podem não ter par 1:1: diga qual código sobrou ou faltou e marque "conferir". Só trate como alteração não salva ou leitura errada a diferença em tipos mapeados como "certo". Se der 429, siga a regra 6 de `disciplina-de-evidencia.md`: leia o `cooldown_seconds` devolvido; se for até 120 segundos, espere esse tempo e repita a mesma chamada uma única vez. Se voltar 429 (ou o cooldown for maior), registre "conector parcial (429, cooldown <N> s)" e siga só com a tela.
5. **Especificação:** compare entrada, cada bloco (pela referência `N`, `N.SIM`, etc.), configurações, saída **e dependências** (a segmentação, o email, o funil citados existem e são os mesmos). Classifique: **ok**, **divergente**, **faltando**, **sobrando**.
   Na entrada, confira o recorte. **Leads que já atendem aos critérios** dispara o fluxo de uma vez, ao ativar, para todo o histórico que atende à entrada (`estrutura-do-fluxo.md`). Se a especificação não pede esse recorte de forma explícita, ou se não há especificação, registre como divergência **alta**. Se pede, registre mesmo assim em Divergências e riscos, com gravidade **risco aceito** e o volume quando a tela ou o conector mostrar (ex.: tamanho da segmentação de entrada), e leve a condição para o veredito.
6. **Regras:** passe pelas 15 regras de desenho e marque cada uma como cumprida, violada ou não se aplica. Toda regra violada entra também em **Divergências e riscos**, com gravidade (alta se dispara errado ou para quem não deve; média nos demais casos), mesmo que a especificação tenha pedido assim.
7. **Relatório** no formato abaixo, com o veredito:
   - **não ativar:** qualquer divergência ou regra violada de gravidade alta;
   - **ativar após correções:** só divergências ou regras violadas de gravidade média ou baixa;
   - **pronto para ativar:** nenhuma divergência e nenhuma regra violada. Se a tela não pôde ser lida, o veredito máximo é "ativar após correções" com a leitura da tela como correção pendente. Conector parcial não impede "pronto" quando a tela foi lida por inteiro.
   Com o recorte **Leads que já atendem aos critérios** pedido pela especificação, o veredito vem com a condição escrita: "<veredito>, depois do aceite do disparo para o histórico (<volume ou "volume não visto">)".

## Formato do relatório

```markdown
# Conferência: <nome do fluxo>
**Status atual:** <ativo|inativo> · **Fontes:** tela <sim|parcial|não>, conector <sim|parcial|não>, especificação <sim|não>

## Veredito: <pronto para ativar | ativar após correções | não ativar>

## Item a item
| Ref. | Esperado | Encontrado | Situação |
|---|---|---|---|

## Divergências e riscos
| Ref. | Esperado | Encontrado | Gravidade | Correção |
|---|---|---|---|---|
(ou "Nenhuma.")

## Regras de desenho
- [x] 1. Espera antes de condição de email: cumprida em <ref>
- [!] 3. Responsável antes da oportunidade: **violada** em <ref>. Correção: <...>
- [-] 5. Qualificação é valor exato: não se aplica (<motivo curto>)

## O que assumi
- <ex.: versão da especificação usada; leitura de um resumo truncado; interpretação de nome>

## Limites e o que não foi visto
- <item, se é não consultado ou não exposto pelo conector, e o próximo passo para fechar>
```

Gravidade: **alta** (dispara errado ou para quem não deve), **média** (perde efeito ou dado), **baixa** (nome, organização), **risco aceito** (pedido pela especificação, mas exige aceite antes de ativar; não rebaixa o veredito, vira a condição dele).

## Limites

- Nunca clique em Salvar, Salvar e Ativar, Ativar ou Desativar. Se precisar abrir um painel, feche sem alterar. Para sair, use **Sair do editor**; se a RD perguntar se quer salvar alterações, escolha sair sem salvar e registre no relatório que o aviso apareceu (pode indicar que um painel foi alterado sem querer).
- Não corrija nada: a correção é da pessoa ou de `rd-automacao-montar` (só em fluxo inativo). Em fluxo ativo, a pessoa decide entre editar o atual ou criar outro e desativar o antigo (regra 15).

## Exemplo de prompt

> Antes de ativar, confere o fluxo "Laboratório de automação" contra a especificação. Está pronto pra ativar?

## Exemplo de retorno

Resumo de uma execução real validada em 30/09/2026 (nomes anonimizados):

> # Conferência: Laboratório de automação
> **Status atual:** inativo · **Fontes:** tela sim, conector parcial (429, cooldown 60 s), especificação sim
>
> ## Veredito: pronto para ativar
>
> | Ref. | Esperado | Encontrado | Situação |
> |---|---|---|---|
> | Entrada | manual ou outro fluxo | "Forem adicionados a partir de outro fluxo... ou inseridos manualmente" | ok |
> | 3 | Dividir caminho por segmentação: Lista de teste | idem | ok |
> | Saída | só final do fluxo | idem | ok |
>
> **Divergências e riscos:** nenhuma.
>
> ## Regras de desenho
> - [x] 12. Saída é parte do desenho: só final do fluxo, como na especificação
> - [x] 14. Ramos não se juntam sozinhos: 3.SIM e 3.NÃO terminam em si, sem Unir caminho previsto
> - [-] 1 a 11, 13 e 15: não se aplicam (fluxo só com tags, espera, divisão por lista e aviso interno; inativo)
>
> **O que assumi:** especificação na versão enviada pela pessoa; tags truncadas no cartão lidas pelo tooltip.
> **Limites e o que não foi visto:** tipos de ação pelo conector (429 no detalhe, repetido uma vez sem sucesso): não consultado, contagem conferida só pela tela; destino do Notificar email conferido só pela tela.
