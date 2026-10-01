# Modelo: passagem do Marketing para o CRM

Para entregar ao time comercial o lead pronto pra conversa, no RD Station CRM, com dono, contexto e tarefa. É um ponto comum de perda de lead: a oportunidade chega sem responsável, sem contexto, ou duplicada.

## Parâmetros que precisam ser respondidos

| Parâmetro | Por que importa |
|---|---|
| O que faz um lead estar pronto | É a entrada: pedido de contato, lead scoring, resposta a uma pergunta, segmentação de qualificados. |
| Funil, etapa e equipe no CRM | Criar Negociação exige que existam. |
| Quem atende | Uma pessoa, uma equipe com rodízio, ou o último responsável se o lead já foi negociação. |
| Prazo do primeiro contato | Vira a tarefa (ex.: ligar em 2 horas). |
| O que o vendedor precisa saber | Vira a anotação: origem, interesse, respostas do formulário. |
| Produto | Se houver, Adicionar produto à Negociação (o produto precisa existir no CRM). |
| O lead pode passar mais de uma vez? | Define reentrada e o risco de negociação duplicada. |

## Estrutura base

```
Entrada: <conversão de pedido de contato | segmentação de qualificados>
Reentrada: mais de uma vez, com "atribuir ao último responsável" marcado e o risco de
           negociação repetida em Decisões em aberto (apenas uma vez só quando
           cada lead puder gerar uma única negociação)
Saída: final do fluxo

1. Distribuir Leads entre os responsáveis: <pessoas da fila>
   (ANTES da oportunidade: o lead chega ao CRM com dono)
2. Marcar Oportunidade
3. Criar Negociação no CRM: funil <X>, etapa <Y>, equipe <Z>
   [x] se o lead já foi negociação, atribuir ao último responsável
4. Atualizar nome da Negociação: "<produto>, *|PRIMEIRO_NOME|*"
5. Adicionar anotação: "Veio pela conversão <nome da conversão>. Curso de interesse: *|<CAMPO_DE_INTERESSE>|*."
   (usar só variáveis que aparecem em "Adicionar variável" na conta)
6. Criar tarefa na Negociação: Ligar, prazo 2 horas, "Primeiro contato"
7. Notificar responsável pelo Lead
```

## Variações

- **Roteamento por produto:** depois de criar a negociação e adicionar o produto, **Dividir caminho por produto** para mandar cada produto a uma etapa (**Mover Negociação no CRM**).
- **Roteamento por qualificação:** **Dividir caminho por qualificação** usa valor exato (1 a 5). Negociação nasce com 1. Para "4 ou mais", são duas divisões.
- **Roteamento por equipe:** **Dividir caminho por equipe** para dar tratamento diferente por time (ex.: tarefa de prazo mais curto para inside sales).
- **Lead frio que volta:** a automação não reabre negociação. **Atualizar status** só oferece Pausada, Perdida ou Vendida, nenhuma divisão lê o status da negociação e **Criar tarefa na Negociação** atua na negociação criada durante o fluxo. **Mover Negociação no CRM** leva a mais recente para outra etapa, mas o levantamento não verificou se isso reativa negociação pausada. Para quem já foi negociação, use **Criar Negociação no CRM** com "se o lead já foi negociação no CRM, atribuir ao último responsável" marcado, e registre em Decisões em aberto que a negociação antiga continua pausada (reabrir é ação manual no CRM).
- **Aviso para gestor:** **Notificar email** com o endereço do gestor (leva só campos padrão do lead).
- **Outro CRM:** **Enviar Leads para Integração** com a URL do sistema; o Marketing manda campos, tags, estágio e scoring.

## Armadilhas

1. **Oportunidade antes do responsável.** O lead chega ao CRM sem dono. A RD avisa isso na própria tela de Distribuir.
2. **Distribuir substitui dono existente** se ele não estiver na fila. Revise a fila antes.
3. **Negociação duplicada** com reentrada ligada. Decida de propósito e marque "atribuir ao último responsável".
4. **Ações que atuam na "negociação mais recente"** (anotação, nome, produto, status, responsável, mover, divisões) mexem na negociação errada se o lead tiver outra mais nova aberta por fora do fluxo.
5. **Mover, atualizar status e atualizar responsável não criam negociação.** Sem Criar Negociação antes (ou uma existente), a RD avisa que a ação não afeta negociação que ainda não existe. O levantamento não executou esse caso: conte com falha silenciosa e confira no CRM.
6. **Variável vazia some** do nome e da anotação. "Interesse: " sozinho confunde o vendedor; escreva frases que façam sentido sem o valor.
7. **Responsável do lead ≠ responsável da negociação.** Notificar responsável pelo Lead usa o do Marketing. Dois rodízios independentes (Distribuir no Marketing e equipe no Criar Negociação) podem escolher pessoas diferentes para o mesmo lead. Para alinhar: com uma pessoa só, use a mesma pessoa nos dois blocos; com rodízio, aceite que o dono do lead e o da negociação podem diferir e avise pela tarefa da negociação, ou divida por critério (segmentação) e defina a mesma pessoa nos dois blocos de cada ramo.
8. **Saída "Ao receber marcação Oportunidade" ligada** deve tirar o lead do fluxo no Marcar Oportunidade (dedução pelo painel Saída): a negociação, a tarefa e o aviso não acontecem. Desligue essa saída neste tipo de fluxo.
9. **Marcar Oportunidade com a integração do CRM ligada** pode enviar o lead ao CRM por conta própria. Antes de somar com Criar Negociação no CRM, confira na conta se não nascem duas negociações.

## Dependências que o conector não confere

- **Usuários do Marketing** (usados em Distribuir e Alterar responsável) não são listados por nenhum conector: confirme os nomes na tela.
- **Variáveis de campo personalizado:** use exatamente o nome que aparece em **Adicionar variável**, no painel da anotação ou do nome da negociação. `contact_custom_fields_list` ajuda a achar o campo, mas não garante o nome da variável.
- **Redesenho de fluxo existente:** `workflow_get_details` só mostra os tipos de ação (sem entrada, configuração, ramos nem ordem); a configuração atual vem da tela do editor. Decida entre editar e substituir (regra 15). Dois fluxos ativos com a mesma entrada duplicam negociações.

## Métricas

- Oportunidades marcadas pelo fluxo (Opções > Estatísticas).
- Tempo até o primeiro contato (tarefas concluídas no prazo, no CRM).
- Negociações criadas × leads que entraram (diferença grande indica falha de dependência ou duplicidade).
