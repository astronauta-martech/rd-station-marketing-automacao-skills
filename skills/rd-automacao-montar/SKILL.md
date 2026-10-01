---
name: rd-automacao-montar
description: "Monta no editor do RD Station Marketing, pelo navegador, um fluxo de automação a partir de uma especificação, bloco a bloco, conferindo cada configuração e salvando sempre INATIVO para revisão humana. Também corrige um fluxo inativo apontado pela conferência. Use quando alguém pedir para montar, configurar ou subir na RD um fluxo que já tem especificação, briefing aprovado ou modelo preenchido. Se ainda não há especificação (só o objetivo), use rd-automacao-briefing antes. Requer um navegador controlável com a sessão da RD aberta; nunca ativa o fluxo."
license: "Licença de Uso Astronauta Martech 1.0. Pode usar e cobrar por serviços feitos com esta skill; não pode vender a skill. Termos completos em LICENSE."
metadata:
  autor: "Astronauta Martech"
  versao: "0.1.0"
  produto: "RD Station Marketing"
  requer: "navegador controlável com sessão do RD Station Marketing"
---

# Montar fluxo no editor

Você opera o editor de fluxos da RD pelo navegador e transforma uma especificação em um fluxo **salvo e inativo**. Quem ativa é sempre uma pessoa, depois de revisar.

## Referências

- `references/formato-especificacao.md`: o que você recebe como entrada.
- `references/estrutura-do-fluxo.md`: seção "Método que funcionou para montar no editor". **Leia antes de abrir o editor.**
- `references/catalogo-acoes.md`: campos de cada ação e onde ela fica no menu.

## Antes de começar (pare se algo falhar)

1. **Especificação completa.** Sem **Decisões em aberto** pendentes que afetem blocos. Se não houver especificação, peça ou use `rd-automacao-briefing` antes.
2. **Conta certa.** Leia o nome da conta no topo do editor e confirme com a pessoa antes do primeiro clique que altera algo.
3. **Dependências existem.** Emails, segmentações, funis, equipes e fluxos de destino citados na especificação precisam existir. Falta de dependência: pare e liste.
4. **Nome combinado.** Use exatamente o **Nome no RD** da especificação; se a conta usa um identificador de demanda (ex.: `#1234`) e ele não estiver no nome, confirme com a pessoa antes de acrescentar. Não acrescente `[RASCUNHO]` nem `NAO ATIVAR`: essa convenção (`estrutura-do-fluxo.md`, método, item 6) vale para fluxo de teste descartável, e a conferência compara o nome com a especificação. Busque o nome na listagem antes de criar: se já existir fluxo com ele, pare e pergunte se é para corrigir o existente (só se estiver inativo, ver item 5) ou criar outro (regra 15).
5. **Só fluxo novo ou fluxo INATIVO.** Esta skill cria fluxo novo ou corrige um fluxo **INATIVO** (ex.: divergências apontadas por `rd-automacao-conferir`): abra pela URL do editor, altere só o que foi indicado (entrada, blocos, configurações) e siga para os passos 5 a 7. Se o fluxo a alterar está **ativo** (ex.: especificação de edição, com `## Situação atual` e blocos **[altera]**/**[remove]**), pare: salvar mexe no que está rodando (regra 15) e esta skill nunca ativa nem desativa. Proponha montar a nova versão como fluxo novo e inativo e deixe para a pessoa desativar o antigo.

## Passo a passo

```
- [ ] 1. Criar o fluxo com o nome (ou abrir o fluxo inativo a corrigir)
- [ ] 2. Definir a entrada
- [ ] 3. Inserir os blocos na ordem, conferindo cada um
- [ ] 4. Configurações e saída
- [ ] 5. Salvar (nunca Salvar e Ativar)
- [ ] 6. Confirmar INATIVO na listagem
- [ ] 7. Relatório
```

1. **Criar:** Automação de Marketing > **Criar fluxo** abre a galeria de Modelos de Automação; use **Criar fluxo em branco** (ou o modelo, se a especificação pedir). O editor pede o nome primeiro.
2. **Entrada:** em **Selecionar uma entrada**, escolha o recorte (vão atender / já atendem) e depois o tipo (9 tipos, ver `estrutura-do-fluxo.md`). Se a especificação diz "manual ou outro fluxo", use **Definir entrada depois**. Se abrir o painel para explorar, remova a condição no X antes de seguir.
3. **Blocos, um de cada vez:**
   1. abra o painel **Ações** (botão **Ações** ou o conector **+** do ponto certo), expanda a categoria e arraste a ação até o conector **+** onde ela entra;
   2. espere a interface estabilizar;
   3. preencha os campos com os valores da especificação;
   4. **confira o texto que aparece no bloco antes de fechar o painel** (o editor às vezes mantém o valor anterior se o painel fecha rápido);
   5. feche o painel do bloco e reabra **Ações**: as categorias abertas continuam abertas, então recolha as que não usa;
   6. localize de novo o próximo conector **+** antes de arrastar: o canvas se reorganiza a cada inserção e às vezes centraliza no bloco novo. Um conector colado na borda do painel de Ações ainda aceita o arraste; se ficar atrás do painel, feche o painel e role o canvas.
   Divisões criam dois ramos (SIM e NÃO), e **Esperar e agendar data e hora** também (ATÉ e DEPOIS): monte um ramo inteiro, depois o outro; ramo marcado `(nada: ...)` na especificação fica vazio. **Unir caminho** entra em modo de seleção: clique na ação de destino destacada (Esc cancela).
4. **Configurações e Saída:** abas no topo. Aplique reentrada, finais de semana e condições de saída da especificação.
5. **Salvar:** botão **Salvar**. A RD mostra uma revisão (reentrada, finais de semana, saída): confira contra a especificação e confirme. **Nunca clique em Salvar e Ativar.**
6. **Confirmar:** anote a URL do editor (`.../editor/<id>`, que aparece depois do primeiro Salvar), saia do editor, busque o fluxo pelo nome na listagem e confirme o status **INATIVO**.
7. **Relatório** no formato abaixo.

## Se algo der errado

- Arraste que não pega pelo centro do cartão: use o controle na borda direita.
- Valor que não fixa: reabra o bloco, preencha de novo, espere e confira.
- Validação que impede salvar (ex.: fluxo sem ação, campo obrigatório vazio): leia a mensagem, corrija e registre no relatório.
- Travou no meio: não recarregue a página sem salvar (perde o desenho). Antes de salvar, acrescente `[INCOMPLETO] ` no início do nome para ninguém ativar por engano. Salve o que existe (inativo), registre onde parou e avise a pessoa. Ao retomar e concluir, tire o prefixo.
- Nunca repita uma ação sem conferir na tela se a primeira já aconteceu.

## Formato do relatório

```markdown
# Montagem: <nome do fluxo>
- **Conta:** <nome exibido> · **Status:** INATIVO (conferido na listagem) · **URL:** <url do editor>
- **Blocos montados:** <N> de <N da especificação>

| Ref. | Ação | Configuração conferida | Situação |
|---|---|---|---|
| 1 | Enviar email | "<email>" | ok |
| 3.NÃO.1 | ... | ... | divergência: <qual> |

**Configurações:** reentrada <...>, finais de semana <...> · **Saída:** <...>
**Revisão da RD ao salvar:** <bateu com a especificação | divergiu: qual>
**Pendências para a pessoa:** <lista>
**Próximo passo:** revisar com `rd-automacao-conferir` e ativar manualmente.
```

## Limites

- Nunca ativa, desativa ou apaga fluxo, e só mexe no fluxo que está montando ou corrigindo, sempre inativo.
- Não cria emails, segmentações, funis ou equipes: aponta como dependência.
- Se a interface estiver diferente do descrito nas referências, confie na tela, adapte e registre a diferença no relatório.

## Exemplo de prompt

> Monta no RD a especificação "Laboratório de automação: marcação e aviso interno" (6 blocos, entrada manual, divisão por uma lista) e deixa inativo pra revisão.

## Exemplo de retorno

Resumo de uma execução real validada em 30/09/2026 (nomes anonimizados):

> # Montagem: Laboratório de automação: marcação e aviso interno
> - **Conta:** conferida no topo do editor · **Status:** INATIVO (conferido na listagem) · **URL:** .../editor/<id>
> - **Blocos montados:** 6 de 6
>
> | Ref. | Ação | Configuração conferida | Situação |
> |---|---|---|---|
> | 1 | Adicionar Tags | lab-skills | ok |
> | 2 | Espera | 0 dia, 0 hora, 5 min | ok |
> | 3 | Dividir caminho por segmentação | Lista de teste | ok |
> | 3.SIM.1 | Adicionar Tags | na-lista-de-teste | ok |
> | 3.SIM.2 | Notificar email | time@empresa.example | ok |
> | 3.NÃO.1 | Adicionar Tags | fora-da-lista | ok |
>
> **Configurações:** conforme a especificação (conferidas na revisão ao salvar) · **Saída:** só ao chegar ao final do fluxo
> **Revisão da RD ao salvar:** bateu com a especificação.
> **Pendências para a pessoa:** nenhuma.
> **Próximo passo:** revisar com `rd-automacao-conferir` e ativar manualmente.
