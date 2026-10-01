# Formato de especificação de fluxo

Toda skill que desenha, monta ou confere um fluxo usa este formato. Ele é o contrato entre quem pede, quem monta (pessoa ou agente no navegador) e quem confere. Escreva de forma que alguém consiga montar o fluxo no editor da RD **sem fazer nenhuma pergunta**.

## Modelo

```markdown
# Especificação: <nome do fluxo>

## Resumo
- **Objetivo:** <o que muda na vida do lead ou do time quando o fluxo funciona>
- **Nome no RD:** <nome exato, seguindo a convenção da conta>
- **Métrica de sucesso:** <o número que diz se funcionou, e onde olhar>

## Entrada
- **Tipo:** <nome exato em Selecionar uma entrada: Converteram no evento | Entraram na lista de segmentação | Foram marcados como oportunidade | Campo do Lead | Eventos CRM (subtipo) | Evento de Integração | Evento de ecommerce | Atividades no ecommerce | Potencial de compra | Definir entrada depois (manual ou por outro fluxo)>
- **Detalhe:** <qual conversão, lista, campo ou evento>
- **Recorte:** <Leads que já atendem aos critérios | Leads que vão atender aos critérios | não se aplica (Definir entrada depois)>

## Configurações
- **Finais de semana nas esperas:** <considerar | não considerar> (<por quê>)
- **Reentrada:** <mais de uma vez | apenas uma vez> (<por quê>)

## Saída
- Ao chegar ao final do fluxo (sempre)
- <Ao receber marcação Oportunidade | Ao registrar as conversões: X, Y | Ao registrar eventos de integração: Z | nenhuma outra>

## Dependências (precisam existir antes de montar)
| Tipo | Nome | Situação |
|---|---|---|
| Email | <nome> | <existe | criar> |
| Segmentação | <nome> | <existe | criar> |
| Tag | <nome> | <criada no fluxo> |
| CRM | <funil > etapa > equipe> | <existe | criar> |

## Percurso
1. **<Nome exato da ação>**: <configuração>
2. **Espera**: 0 dia, 1 hora, 0 min
3. **Dividir caminho por email do fluxo**: Abriu "<email do passo 1>"
   - **3.SIM**
     1. **<ação>**: <configuração>
   - **3.NÃO**
     1. **<ação>**: <configuração>
     2. **Unir caminho** → 3.SIM.1

## Regras conferidas
- [x] <regra de desenho aplicada e onde>
- [ ] <regra que não se aplica ou ficou pendente, e por quê>

## Decisões em aberto
- <o que precisa de resposta humana antes de montar>
```

## Fluxo novo ou edição

- **Fluxo novo:** use o modelo acima.
- **Edição de fluxo existente:** acrescente, logo depois do Resumo, a seção `## Situação atual` (o que o fluxo faz hoje, com a fonte: tela do editor ou conector) e, no Percurso, marque cada bloco como **[mantém]**, **[novo]**, **[altera]** ou **[remove]**. Registre em Decisões em aberto se é melhor editar o fluxo ou criar outro e desativar o antigo (regra 15).

## Seções opcionais

Use só quando ajudarem quem monta ou quem aprova, depois de Decisões em aberto:
- `## Linha do tempo`: datas e horários absolutos, para réguas com data.
- `## Quem recebe o quê`: tabela por momento de entrada ou por ramo.
- `## Notas de conteúdo`: regras de conteúdo que mudam o funcionamento (ex.: "email 02 com um único link no corpo, porque a condição de clique não escolhe o link").
- `## Padrões aplicados`: decisões tomadas sem resposta da pessoa.

## Convenções

- **Nome da ação em negrito e exatamente como aparece no menu Ações do editor** (ver catálogo). Isso permite achar o bloco na tela e conferir depois.
- **Ramo que só termina** é escrito `1. (nada: <motivo>)`. O motivo é obrigatório: ramo vazio sem motivo parece esquecimento.
- **Ramos profundos:** a partir do terceiro nível de divisão, dê um rótulo curto ao ramo e use o rótulo nas referências, em vez de numerações como `3.SIM.2.ATÉ.5.NÃO`. Exemplo: `- **3.SIM.2.ATÉ** [R1: lembrete]` e depois `Unir caminho → R1.2`.
- **Numeração por caminho:** divisões abrem `N.SIM` / `N.NÃO`. Espera por data abre `N.ATÉ` / `N.DEPOIS`. Dentro de cada ramo a numeração recomeça em 1. Uma referência como `3.NÃO.2` aponta um bloco sem ambiguidade.
- **Unir caminho** indica o destino com `→` e a referência do bloco.
- **Valores reais, nunca "configurar depois".** Se o valor não é conhecido, ele vai para **Decisões em aberto** e o bloco fica marcado com `(pendente: ...)`.
- **Texto com variável** mostra a variável como na RD: `*|PRIMEIRO_NOME|*`.
- **Uma espera antes de toda condição de email**, sempre explícita no percurso.
- **Regras conferidas** lista as regras de desenho da `estrutura-do-fluxo.md` que se aplicam ao fluxo, marcadas.

## Exemplo curto

```markdown
# Especificação: Confirmação de inscrição no webinar

## Resumo
- **Objetivo:** todo inscrito recebe o acesso na hora e um lembrete no dia.
- **Nome no RD:** [Webinar] 2026-10-15 | Confirmação e lembrete
- **Métrica de sucesso:** abertura do lembrete do dia, comparada com a média dos outros emails de automação da conta (métricas de email do fluxo, na tela da RD ou, com o conector, `emails_by_workflow_analytics`, plano Pro ou superior).

## Entrada
- **Tipo:** Converteram no evento
- **Detalhe:** webinar-2026-10-15-inscricao
- **Recorte:** Leads que vão atender aos critérios

## Configurações
- **Finais de semana nas esperas:** considerar (o evento tem data fixa)
- **Reentrada:** apenas uma vez (quem se inscreve duas vezes não precisa de dois lembretes)

## Saída
- Ao chegar ao final do fluxo (sempre)

## Dependências
| Tipo | Nome | Situação |
|---|---|---|
| Email | [Webinar 15/10] Confirmação | criar |
| Email | [Webinar 15/10] Lembrete do dia | criar |

## Percurso
1. **Enviar email**: "[Webinar 15/10] Confirmação"
2. **Esperar e agendar data e hora**: 15/10/2026 às 09:00
   - **2.ATÉ**
     1. **Enviar email**: "[Webinar 15/10] Lembrete do dia"
   - **2.DEPOIS**
     1. (nada: inscrito depois das 9h já recebeu a confirmação com o acesso)

## Regras conferidas
- [x] Espera por data tem os dois ramos desenhados.
- [x] Fuso da conta conferido antes de marcar 9h.
- [ ] Condição de email: não se aplica.

## Decisões em aberto
- Nenhuma.
```
