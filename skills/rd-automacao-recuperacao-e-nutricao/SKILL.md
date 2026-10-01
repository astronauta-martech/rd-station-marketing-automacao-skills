---
name: rd-automacao-recuperacao-e-nutricao
description: "Desenha fluxos de recuperação e nutrição no RD Station Marketing: reenvio para quem não abriu, reforço para quem abriu e não clicou, retirada de quem já comprou e sequências de nutrição por interesse até o próximo passo comercial. Use quando alguém falar em recuperar leads, reengajar, \"quem não abriu\", \"quem não clicou\", nutrição, sequência de emails, cadência, lead frio ou tirar comprador da régua. Entrega uma especificação pronta para montar, com saídas e tags de controle."
license: "Licença de Uso Astronauta Martech 1.0. Pode usar e cobrar por serviços feitos com esta skill; não pode vender a skill. Termos completos em LICENSE."
metadata:
  autor: "Astronauta Martech"
  versao: "0.1.0"
  produto: "RD Station Marketing"
---

# Recuperação e nutrição

Você identifica qual das quatro situações do modelo a pessoa precisa (não abriu, não clicou, já comprou, nutrição por interesse), combina as que forem necessárias e entrega uma especificação no formato de `references/formato-especificacao.md`.

## Referências

- `references/modelo-recuperacao-e-nutricao.md`: as quatro situações, parâmetros, armadilhas e métricas. **Leia primeiro.**
- `references/formato-especificacao.md`: formato de saída.
- `references/catalogo-acoes.md`: nomes exatos das ações.
- `references/estrutura-do-fluxo.md`: saída, reentrada e as 15 regras de desenho (preenchem "Regras conferidas" no passo 7).
- `references/conector-rd-marketing.md` e `references/codigos-de-acao.md`: o que o conector mostra (e não mostra) de um fluxo, limites de chamada e tradução dos códigos de ação. **Leia antes de chamar qualquer ferramenta de fluxo.**
- `references/disciplina-de-evidencia.md`: como escrever números, níveis de certeza e recomendações que mexem em fluxo. **Aplique antes de entregar.**

## Passo a passo

1. **Classifique o pedido** nas quatro situações. Um pedido de "nutrição" quase sempre precisa também da situação 3 (tirar quem comprou ou já é cliente). Se o email principal for de confirmação (double opt-in), o sinal é **Clicou**, não **Abriu**.
2. **Defina o próximo passo desejado** (compra, matrícula, reunião, resposta). Ele define a **saída** do fluxo. Sem ele, pergunte.
3. **Defina quem já converteu:** qual segmentação ou conversão identifica. Sem isso, a saída fica pendente e deve ir para **Decisões em aberto**.
4. **Monte o percurso** combinando os blocos do modelo. Regras obrigatórias:
   - espera explícita antes de cada **Dividir caminho por email do fluxo**;
   - a condição aponta para um email **do próprio fluxo**;
   - no máximo 3 toques sem reação antes de encerrar o ramo;
   - toda tag adicionada tem um ponto onde é removida, ou a especificação diz por que fica;
   - depois de cada divisão, diga onde os ramos voltam a se juntar com **Unir caminho** → <referência do bloco>, ou escreva `(nada: motivo)` no ramo que termina.
5. **Configure a saída** (painel Saída) com a conversão do objetivo, além do final do fluxo. O painel Saída só aceita conversão, marcação de oportunidade, evento de integração ou evento de ecommerce: se "quem já converteu" só existe como segmentação, use **Dividir caminho por segmentação** antes de cada envio comercial (situação 3 do modelo). Se o próximo passo não gera nenhum desses sinais (ex.: resposta por email), não há saída automática: registre em **Decisões em aberto**.
6. **Proponha a janela de envio** com **Esperar e agendar hora** quando o público for de horário comercial.
7. **Preencha Regras conferidas** com as regras de `references/estrutura-do-fluxo.md` que se aplicam (normalmente 1, 2, 12 e 14; 15 se for edição; e as das ações usadas, como 6 para **Adicionar Leads a outros fluxos**, 9 para **Marcar Venda** e 13 para **Marcar Oportunidade**) e confira ali também as 5 armadilhas do modelo. Depois entregue.

## Quando já existe um fluxo

A recuperação de quem não abriu **entra no fluxo existente** (a condição só enxerga emails do próprio fluxo). Nesse caso, a especificação é de **edição** (ver "Fluxo novo ou edição" no formato). Com o conector do RD Station Marketing:

1. `workflows_search` para achar o fluxo e `workflow_get_details` para saber **quais tipos de ação** ele tem (códigos de 5 letras, como `SDEMA` para **Enviar email** e `WTDEL` para **Espera**; tradução em `references/codigos-de-acao.md`). Esse retorno não traz entrada, configuração, ramos nem a ordem do percurso: a `## Situação atual` sai da tela do editor (print ou navegador). Sem a tela, escreva a Situação atual como "parcial (conector): só os tipos de ação" e ponha o percurso atual em **Decisões em aberto**. Se der 429, siga a regra 6 de `references/disciplina-de-evidencia.md` (espere o `cooldown_seconds` devolvido, se for até 120 segundos, e repita a mesma chamada uma única vez); se persistir, siga sem a estrutura e diga isso.
2. `emails_by_workflow_analytics` (plano Pro ou superior) para abertura, clique e bounce. Comece por 30 dias; **se houver menos de 50 contatos, amplie para 12 meses** e informe sempre o n. O mesmo email em duas ações aparece duas vezes: aponte. Se der erro de plano ou a ferramenta falhar duas vezes (ela pode dar timeout), escreva "métricas de email: não consultadas" e peça os números da tela do fluxo. Não estime.
3. `landing_pages_search` e `forms_search` para achar as conversões de "já converteu" (saída). Conversão que chega de fora dos formulários e landing pages da RD (site próprio, integração) não aparece ali: se não achar, pergunte o identificador.
4. Diga o número que motivou cada recomendação. Se o volume for baixo demais para medir efeito, diga isso antes de desenhar.

## Limites

- Não escreva o conteúdo dos emails na especificação; uma linha de objetivo por email. Assunto novo para a recuperação é recomendação, não texto pronto.
- Não prometa melhora de taxa. Diga qual métrica vai mostrar se funcionou.
- Não desenhe a passagem para o CRM aqui. Se o ramo SIM levar ao comercial, termine com **Adicionar Leads a outros fluxos** apontando para o fluxo de passagem (o destino precisa estar ativo; regra 6) e indique `rd-automacao-passagem-para-crm`.
- A especificação não cria nada na conta. Para montar, use `rd-automacao-montar`.

## Exemplo de prompt

> Nosso fluxo de confirmação da newsletter manda um email de boas-vindas. Quero recuperar quem não abriu e nutrir quem abriu até pedir uma reunião. Olha os números antes.

## Exemplo de retorno

Resumo ilustrativo, baseado em uma execução validada em 30/09/2026 (nomes e números alterados):

> **Antes do desenho, os números** (métricas de email do fluxo, 12 meses): 9 inscrições (n=9, pequeno demais para concluir), o mesmo email sai por **duas ações** do fluxo e 4 de 11 envios deram hard bounce. O gargalo é a entrada, não a abertura.
>
> Especificação de **edição** do fluxo atual (a condição só enxerga emails do próprio fluxo):
> 1. [mantém] **Enviar email**: "01"
> 2. [novo] **Esperar e agendar hora**: 2 dias, 10:00 a 12:00
> 3. [novo] **Dividir caminho por email do fluxo**: Abriu "01"
>    - **3.SIM**
>      1. [novo] **Esperar e agendar hora**: 2 dias, 10:00 a 12:00 (início da nutrição)
>      2. ...
>    - **3.NÃO**
>      1. [novo] **Enviar email**: "01R" (assunto novo)
>      2. [novo] **Unir caminho** → 3.SIM.1
>
> **Decisões em aberto:** o email 01 é de confirmação (double opt-in)? Se for, a divisão é por Clicou. A conversão de pedido de reunião não foi encontrada no conector.
