---
name: rd-automacao-briefing
description: "Transforma um objetivo de automação em uma especificação completa de fluxo do RD Station Marketing, pronta para montar no editor sem nenhuma pergunta. Use quando alguém descrever o que quer que aconteça com os leads (\"quero mandar um email pra quem baixou o ebook e avisar o vendedor se clicar\", \"preciso de um fluxo de boas-vindas\") ou pedir para desenhar, planejar, especificar ou revisar a lógica de um fluxo antes de montar. Aplica padrões seguros no que falta, deixa em Decisões em aberto o que muda o desenho e entrega entrada, configurações, saída, dependências, percurso bloco a bloco e regras conferidas. Para régua de evento, recuperação e nutrição ou passagem para o CRM, prefira as skills específicas."
license: "Licença de Uso Astronauta Martech 1.0. Pode usar e cobrar por serviços feitos com esta skill; não pode vender a skill. Termos completos em LICENSE."
metadata:
  autor: "Astronauta Martech"
  versao: "0.1.0"
  produto: "RD Station Marketing"
---

# Briefing de fluxo de automação

Você transforma um pedido solto em uma **especificação de fluxo** no formato de `references/formato-especificacao.md`. A especificação é o contrato: quem monta (pessoa ou agente) não deve precisar perguntar nada.

## Referências

- `references/formato-especificacao.md`: o formato de saída, com exemplo. **Leia antes de escrever.**
- `references/catalogo-acoes.md`: as 38 ações (4 dependem do plano), campos e cuidados. Use os nomes exatos da tabela do topo, que são os do menu Ações.
- `references/estrutura-do-fluxo.md`: entrada, reentrada, saída e as 15 regras de desenho.
- `references/disciplina-de-evidencia.md`: como escrever o que veio da conta. **Leia só se usar o conector** (passo 5) e, nesse caso, acrescente ao fim da especificação as seções "O que assumi" e "Limites e o que não foi visto" (item 8). Sem conector, não se aplica.

## Passo a passo

1. **Entenda o objetivo.** Reescreva em uma frase o que muda para o lead ou para o time quando o fluxo funciona. Se o pedido é um modelo que tem skill própria (evento com data: `rd-automacao-regua-de-evento`; quem não abriu, quem não clicou ou nutrição: `rd-automacao-recuperacao-e-nutricao`; do Marketing ao CRM: `rd-automacao-passagem-para-crm`), diga isso e use a skill específica: esta skill não traz os modelos. Siga aqui só se ela não estiver instalada, avisando que o desenho não parte do modelo.
2. **Levante o que falta.** Compare o pedido com as seções do formato (entrada, configurações, saída, dependências, percurso). Se o próprio objetivo não está claro (não dá para dizer quem entra nem o que deve acontecer), pare e pergunte. Nos demais casos, **não pare**: entregue a especificação com os padrões seguros da tabela abaixo (listados em **Padrões aplicados**), marque com `(pendente: ...)` os blocos cujo valor ninguém informou (identificador de conversão, caixa de email, nome de lista) e leve para **Decisões em aberto** esses valores e as escolhas que mudam o desenho. Escolhas de desenho: no máximo 5, cada uma com a sugestão que você aplicou:
   > "Quem já comprou deve sair do fluxo? Apliquei que sim, pela aba Saída, com a conversão de compra."
3. **Desenhe o percurso** bloco a bloco com os nomes exatos do catálogo. Aplique as regras de desenho, principalmente:
   - espera antes de qualquer condição de email;
   - responsável definido antes de Marcar Oportunidade;
   - ramo "depois do dia" em toda espera por data;
   - ações de CRM só depois de existir negociação.
4. **Liste as dependências.** Tudo que precisa existir antes de montar: emails, segmentações, conversões, funis, etapas, equipes, produtos, fluxos de destino, URLs.
5. **Confira a conta, se houver conector** do RD Station Marketing:
   - dependências: `emails_search` com `types: ["WORKFLOW_EMAIL"]` e o nome em `query`; `segmentation_list`; `landing_pages_search` e `forms_search` (`search` com o título; trazem os identificadores de conversão para entrada e saída). Marque cada uma como "existe", "criar" ou "conferir". Use "conferir" quando a busca não é conclusiva: `segmentation_list` pode não trazer todas as listas, e conversão enviada por API não aparece como formulário nem landing page (pergunte o identificador);
   - **concorrência:** `workflows_search` (`search` com palavras do tema e do material). O conector devolve só nome e status, **não a entrada**: um fluxo ativo com nome parecido é concorrente "(pelo nome)". Aponte em Decisões em aberto e peça para conferir a entrada no editor antes de ativar, porque dois fluxos ativos com a mesma entrada fazem o lead receber os dois.
   Sem conector, marque tudo como "conferir".
6. **Confira as regras.** Preencha **Regras conferidas** com as regras da `estrutura-do-fluxo.md` que se aplicam. Uma regra violada volta para o passo 3.
7. **Entregue a especificação** com uma linha de contexto antes. **Padrões aplicados** entra sempre que um padrão foi usado sem resposta da pessoa; as outras seções opcionais do formato (linha do tempo, notas de conteúdo) entram quando ajudam quem monta. Se o pedido é revisar ou mudar um fluxo que já existe, use a variante "Edição de fluxo existente" do formato: seção Situação atual (com a fonte: tela do editor ou descrição da pessoa, porque o conector não mostra entrada nem ramos), blocos marcados [mantém], [novo], [altera] ou [remove], e a decisão da regra 15 em Decisões em aberto.

## Excluir um público

A aba Saída não aceita segmentação. Para que um público (ex.: clientes) não receba parte do fluxo, use **Dividir caminho por segmentação** logo no início e decida se esse público recebe ao menos o que pediu (ex.: o material) antes de sair. Se a pessoa não disse, aplique "recebe o que pediu e sai" e registre em Decisões em aberto.

## Padrões seguros (quando falta a resposta)

| Decisão | Padrão | Motivo |
|---|---|---|
| Reentrada | Apenas uma vez | Evita envio duplicado e negociação repetida. |
| Finais de semana | Considerar | É o padrão da RD; mude só se o público não lê no fim de semana. |
| Saída | Final do fluxo + conversão do objetivo | Quem já fez o que o fluxo pede não recebe mais nada. |
| Espera antes de avaliar abertura | 1 a 2 dias | Tempo razoável para abrir. |
| Janela de envio | Esperar e agendar hora, 10:00 a 12:00 | Horário comercial, sem madrugada. |
| Recorte da entrada | Leads que vão atender aos critérios | "Já atendem" dispara para o histórico inteiro de uma vez. |
| Quem não abriu | Um reenvio com assunto novo, depois segue | Uma segunda chance sem insistir. |
| Saída por conversão num fluxo que avisa alguém | Só se a conversão já avisa por outro caminho | Senão o lead sai antes do aviso. |

## Limites

- Não invente ações, variáveis ou opções que não estão no catálogo. Se o objetivo exige algo que o editor não tem, diga isso e proponha a alternativa mais próxima.
- WhatsApp, SMS, Mensagem Inteligente e Teste A/B dependem do plano e podem estar bloqueados na conta: marque como dependência "conferir".
- A especificação não cria nada na conta. Para montar, use a skill `rd-automacao-montar`.

## Exemplo de prompt

> Quero um fluxo pra quem baixar o guia "IA na captação de alunos": entregar o material, ver se abriu e, se clicar no link do diagnóstico gratuito, avisar o comercial. Quem já é cliente não deveria receber.

## Exemplo de retorno

Trecho de uma execução de teste de 30/09/2026 (pedido fictício, nomes simplificados). Resumo, Dependências e Regras conferidas ficaram de fora para caber aqui; na entrega, siga o formato inteiro.

> # Especificação: Guia IA na captação, entrega e aviso ao comercial
>
> ## Entrada
> - **Tipo:** conversão em evento
> - **Detalhe:** formulário do guia (pendente: identificador da conversão)
> - **Recorte:** leads que vão atender
>
> ## Configurações
> - **Finais de semana nas esperas:** considerar (padrão da RD)
> - **Reentrada:** apenas uma vez (quem baixa de novo não recebe o convite duas vezes)
>
> ## Saída
> - Ao chegar ao final do fluxo (sempre)
>
> ## Percurso
> 1. **Enviar email**: "01 - Entrega do guia"
> 2. **Dividir caminho por segmentação**: Clientes
>    - **2.SIM**
>      1. (nada: cliente recebe só o material)
>    - **2.NÃO**
>      1. **Esperar e agendar hora**: 2 dias, 10:00 a 12:00
>      2. **Dividir caminho por email do fluxo**: Abriu "01 - Entrega do guia"
>         - **2.NÃO.2.SIM** [R1: convite]
>           1. **Enviar email**: "02 - Convite pro diagnóstico"
>           2. **Espera**: 2 dias, 0 hora, 0 min
>           3. **Dividir caminho por email do fluxo**: Clicou "02 - Convite pro diagnóstico"
>              - **R1.3.SIM**
>                1. **Adicionar Tags**: guia-ia-captacao-clicou-diagnostico
>                2. **Notificar email**: (pendente: caixa do comercial)
>              - **R1.3.NÃO**
>                1. (nada: não clicou no convite)
>         - **2.NÃO.2.NÃO**
>           1. **Enviar email**: "01b - Reenvio do guia" (assunto novo)
>           2. **Esperar e agendar hora**: 2 dias, 10:00 a 12:00
>           3. **Unir caminho** → R1.1
>
> ## Decisões em aberto
> - Identificador da conversão do formulário do guia.
> - Caixa do comercial para o **Notificar email**.
> - A conversão do diagnóstico já avisa o comercial por outro caminho? Se sim, ligar a saída por essa conversão.
> - Há fluxo ativo com nome parecido (`workflows_search`, pelo nome)? Se houver, conferir a entrada dele no editor antes de ativar.
>
> ## Notas de conteúdo
> - O email 02 leva um único link, o do diagnóstico (a condição de clique não escolhe o link).
>
> ## Padrões aplicados
> - Reentrada apenas uma vez; recorte "vão atender"; espera de 2 dias na janela de 10:00 a 12:00; um reenvio com assunto novo para quem não abriu.
