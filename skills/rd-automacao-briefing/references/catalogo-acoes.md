# Catálogo de ações da automação do RD Station Marketing

Levantamento feito em setembro de 2026 no editor de fluxos do RD Station Marketing (Automação de Marketing > Fluxos de Automação), bloco a bloco: cada ação foi arrastada para um fluxo de teste inativo, aberta e registrada com os campos e os avisos que a própria interface mostra.

Como ler este catálogo:

- **O que faz** e **Campos** descrevem a tela de configuração.
- **A RD avisa** resume os alertas e dicas exibidos no painel da ação.
- **Cuidados** são regras de desenho que saem desses avisos ou do teste.
- **Verificado** indica até onde o teste foi. Nenhuma ação foi executada com lead real. Quatro (Adicionar Tags, Espera, Dividir caminho por segmentação e Notificar email) foram executadas com um contato de teste num fluxo de laboratório, em 30/09/2026; as demais descrevem configuração, não resultado de execução.
- A interface da RD muda com o tempo e varia por plano. Se algo aqui não bater com a tela, vale a tela.

São 38 ações em 7 categorias. 34 foram inseridas num fluxo de teste e abertas: 27 configuradas por completo (ou sem campo a preencher) e 7 só inseridas, com a seleção pendente (fluxo, pessoa, produto ou base legal; veja **Verificado** em cada uma). As outras 4 (WhatsApp, SMS, Mensagem Inteligente e Teste A/B) dependem do plano ou da contratação: apareceram bloqueadas numa conta e liberadas em outra.

Os nomes abaixo são os do painel **Ações** do editor, que é onde a pessoa procura. O título dentro do painel de configuração às vezes varia um pouco (ex.: "Remover Lead de outros fluxos" no menu, "Remover Leads de outros fluxos" no painel; "Atualizar status" no menu, "Atualizar status da Negociação" no painel). Ao citar, use o nome do menu; ao conferir, aceite a variação.

| Categoria | Ações (nome no menu) |
|---|---|
| Comunicação | Enviar email; Enviar WhatsApp*; Enviar SMS*; Enviar Mensagem Inteligente* |
| Espera | Espera; Esperar e agendar hora; Esperar e agendar data e hora |
| Caminho do Lead | Teste A/B*; Adicionar Leads a outros fluxos; Remover Lead de outros fluxos; Dividir caminho por email do fluxo; Dividir caminho por segmentação; Unir caminho |
| RD Station CRM | Criar Negociação no CRM; Adicionar anotação; Criar tarefa na Negociação; Atualizar nome da Negociação; Atualizar tarefa; Adicionar produto à Negociação; Atualizar responsável; Atualizar status; Mover Negociação no CRM; Dividir caminho por produto; Dividir caminho por qualificação; Dividir caminho por equipe |
| Integrações | Enviar Leads para Integração |
| Gerenciar Lead | Adicionar Tags; Remover Tag; Adicionar Base Legal; Remover Base Legal; Marcar Oportunidade; Desmarcar Oportunidade; Alterar estágio dos Leads; Marcar Venda |
| Responsável e Notificação | Alterar responsável pelos Leads; Distribuir Leads entre os responsáveis; Notificar email; Notificar responsável pelo Lead |

\* Disponibilidade depende do plano ou da contratação da conta.

---

## Comunicação

### Enviar email

- **O que faz:** envia ao lead um email de automação já criado na conta.
- **Campos:** seleção de um email existente (busca por nome).
- **Cuidados:** o email precisa existir antes; o fluxo não cria email. Se o próximo passo for avaliar abertura ou clique, coloque uma **Espera** entre o envio e a condição, senão quase ninguém terá aberto ainda.
- **Verificado:** configurado duas vezes no teste (confirmação e recuperação).

### Enviar WhatsApp, Enviar SMS, Enviar Mensagem Inteligente

- **Situação:** numa conta apareceram **não arrastáveis**; em outra, arrastáveis e com selos "Novo", "Conheça" e "Novidade". A disponibilidade depende do plano ou da contratação. Não foram configuradas no levantamento.
- **Cuidados:** confirmar na conta de destino antes de desenhar um fluxo que dependa delas. Se o bloco não arrasta nem pelo controle da borda direita, é provável que a conta não tenha o recurso contratado ou que o usuário não tenha permissão: confirme com quem administra a conta.
- **Verificado:** presença no catálogo e disponibilidade variando por conta.

---

## Espera

### Espera

- **O que faz:** segura o lead por um tempo relativo antes da próxima ação.
- **Campos:** dias, horas e minutos.
- **A RD avisa:** considerar ou não os finais de semana é uma opção das **Configurações do fluxo**, não do bloco.
- **Cuidados:** use antes de condições de abertura ou clique. Com os finais de semana desconsiderados, o texto da RD diz que os leads esperam até segunda-feira para seguir. O esperado é que isso afete só a espera que terminaria no sábado ou no domingo (leitura da interface; não medido).
- **Verificado:** configurada (1 hora; e 5 minutos em um fluxo salvo). Executada com contato de teste num fluxo de laboratório: a espera de 5 minutos segurou o contato até o fim do tempo.

### Esperar e agendar hora

- **O que faz:** espera um número de dias e executa a próxima ação dentro de uma janela de horário.
- **Campos:** quantidade de dias; faixa de horário (ex.: 10:00 a 12:00).
- **A RD avisa:** a contagem de dias **não** é feita em blocos de 24 horas completas (há artigo de ajuda da RD sobre o cálculo).
- **Cuidados:** bom para mandar mensagem em horário comercial. Não prometa "exatamente 24 horas depois".
- **Verificado:** configurada (após 1 dia, entre 10h e 12h).

### Esperar e agendar data e hora

- **O que faz:** segura o lead até uma data e hora fixas.
- **Campos:** data (calendário, não permite datas passadas); hora (hh:mm).
- **A RD avisa:** quem chegar ao bloco **depois** da data segue direto por um **caminho alternativo**. O bloco cria duas saídas: "até o dia" e "depois do dia". O disparo usa o **fuso horário configurado na conta** (Configurações > Visão Geral).
- **Cuidados:** desenhe sempre o ramo "depois do dia"; é por ele que passam os inscritos atrasados. Confira o fuso da conta antes de marcar horário de evento.
- **Verificado:** configurada, com as duas saídas criadas.

---

## Caminho do Lead

### Dividir caminho por email do fluxo

- **O que faz:** divide o caminho em SIM e NÃO conforme o lead **abriu** ou **clicou** num email.
- **Campos:** evento (Abriu o email do fluxo / Clicou no email do fluxo); qual email **deste fluxo**.
- **Cuidados:** só aceita emails enviados pelo próprio fluxo. Coloque uma Espera (qualquer um dos três tipos) antes. Planeje as duas saídas: o NÃO costuma virar recuperação.
- **Clique:** o levantamento não mostrou opção de escolher **qual link** foi clicado. Trate "Clicou" como "clicou em qualquer link do email": se o clique num link específico importa, faça um email com um único link no corpo.
- **Momento da avaliação:** a divisão avalia o lead quando ele chega ao bloco. Quem abrir ou clicar depois já seguiu pelo NÃO (comportamento provável, não medido).
- **Verificado:** configurada com "Abriu". A opção "Clicou" foi vista, não configurada.

### Dividir caminho por segmentação

- **O que faz:** divide em SIM e NÃO conforme o lead faz parte de uma segmentação (lista) existente.
- **Campos:** uma segmentação.
- **Cuidados:** a segmentação precisa existir e representar exatamente a condição desejada (ex.: "compradores"). A qualidade da divisão é a qualidade da lista.
- **Verificado:** configurada. Executada com contato de teste num fluxo de laboratório: o contato, que estava na lista, seguiu pelo SIM.

### Adicionar Leads a outros fluxos

- **O que faz:** coloca o lead em um ou mais fluxos existentes.
- **Campos:** um ou mais fluxos de destino.
- **A RD avisa:**
  - as **condições de entrada do fluxo de destino não são consideradas**;
  - o lead **não é adicionado se o destino estiver desativado** no momento da ação;
  - se o lead já percorreu o destino, **percorre de novo**.
- **Cuidados:** é a forma de encadear fluxos. Cuidado com reenvio: o lead refaz o destino inteiro.
- **Verificado:** bloco inserido; destino **não selecionado** (seleção pendente).

### Remover Lead de outros fluxos

- **Nome no painel de configuração:** Remover Leads de outros fluxos.
- **O que faz (texto do painel da RD):** impede o lead de percorrer os fluxos escolhidos até terminar o caminho do fluxo atual. O efeito não foi executado com lead no levantamento: confira na conta antes de depender dele.
- **Campos:** um ou mais fluxos, ou a opção "remover de todos os outros fluxos de automação".
- **Cuidados:** use para evitar que o lead receba duas comunicações concorrentes ao mesmo tempo. "Todos" é amplo: afeta fluxos de outras equipes.
- **Verificado:** bloco inserido; fluxos **não selecionados** (seleção pendente).

### Unir caminho

- **O que faz:** liga o fim de um ramo a uma ação de outro ramo, para os dois seguirem juntos.
- **Campos:** não abre formulário. Ao inserir, o editor entra em **modo de seleção** e destaca as ações possíveis; clicar em uma conclui a união. **Esc** cancela.
- **Cuidados:** use para não duplicar sequências iguais em ramos diferentes.
- **Verificado:** união concluída entre o ramo "até o dia" e o ramo "depois do dia" de uma espera por data.

### Teste A/B

- **Situação:** bloqueado numa conta, identificado como recurso do **Plano Advanced**; arrastável em outra. Não configurado.
- **Verificado:** presença no catálogo e disponibilidade variando por plano.

### Divisões com mais de dois caminhos

Toda divisão configurada no levantamento tem só **SIM** e **NÃO** (e a espera por data, **ATÉ** e **DEPOIS**); o Teste A/B não foi aberto. Para três ou mais caminhos, encadeie outra divisão dentro do NÃO. Depois de uma divisão, os ramos **não se juntam sozinhos**: tudo o que vem depois fica dentro de cada ramo, a menos que se use **Unir caminho**.

---

## Gerenciar Lead

### Adicionar Tags

- **O que faz:** adiciona tags ao lead.
- **Campos:** até **15 tags** por bloco; busca ou cria na hora.
- **Cuidados:** combine uma convenção de nomes antes (ex.: `produto-estado`), porque tags criadas no fluxo viram parte da base.
- **Verificado:** configurada. Executada com contato de teste num fluxo de laboratório: a tag apareceu no contato segundos depois da inscrição.

### Remover Tag

- **O que faz:** remove **uma** tag do lead.
- **Campos:** nome da tag (texto).
- **Cuidados:** um bloco por tag. Use para limpar o estado anterior (ex.: tirar "pendente" quando virar "comprador").
- **Verificado:** configurada.

### Alterar estágio dos Leads

- **O que faz:** muda o estágio do funil do lead.
- **Campos:** Lead, Lead Qualificado ou Cliente.
- **Cuidados:** coloque depois de uma condição que justifique o estágio.
- **Verificado:** configurada (Cliente).

### Marcar Venda

- **O que faz:** marca o lead como venda; fica registrado no perfil e nas estatísticas do fluxo.
- **Campos:** nenhum.
- **A RD avisa:** as qualificações, oportunidades e vendas do fluxo aparecem em **Opções > Estatísticas**.
- **Cuidados:** o bloco **não verifica** se houve compra. Só deve vir depois de uma evidência (segmentação de compradores, evento de venda).
- **Verificado:** inserida.

### Marcar Oportunidade

- **O que faz:** marca o lead como oportunidade; registra no perfil e nas estatísticas.
- **Campos:** nenhum.
- **Cuidados:**
  - Se a oportunidade precisa chegar ao CRM com dono, **distribua ou defina o responsável antes** (ver Distribuir Leads).
  - Com a saída "Ao receber marcação Oportunidade" ligada, o lead deve **sair do fluxo neste bloco** e não receber o que vem depois (dedução pela opção de saída; não testado com lead). Ou desligue essa saída, ou coloque Marcar Oportunidade por último.
  - O aviso da RD em Distribuir ("para que seja enviado ao CRM") indica que a marcação leva o lead ao CRM quando a integração está ligada. O levantamento **não verificou** se isso cria negociação. Antes de combinar Marcar Oportunidade com **Criar Negociação no CRM**, confira na conta se não nascem duas negociações.
- **Verificado:** inserida.

### Desmarcar Oportunidade

- **O que faz:** tira a marcação de oportunidade do lead.
- **Campos:** motivo (texto).
- **Cuidados:** escreva um motivo que explique o encerramento; ele fica no histórico.
- **Verificado:** configurada.

### Adicionar Base Legal

- **O que faz:** registra uma base legal (LGPD) para coletar e usar os dados do lead.
- **Campos:** uma base. "Mais comuns": Consentimento, Legítimo interesse, Contrato pré-existente. "Outras": Obrigação legal/processo judicial/proteção ao crédito, Interesse vital ou tutela da saúde, Interesse público.
- **Cuidados:** escolher a base é decisão jurídica da empresa, não do fluxo. O catálogo registra as opções da interface; não é orientação jurídica.
- **Verificado:** opções abertas e registradas; **nenhuma selecionada**.

### Remover Base Legal

- **O que faz:** remove a base legal de **comunicação** do lead; fica registrado no perfil.
- **Campos:** nenhum.
- **A RD avisa:** é uma ação ligada às leis de proteção de dados (há artigo de ajuda).
- **Cuidados:** na prática, tira o lead da comunicação. Use em fluxos de descadastro ou pedido de exclusão, nunca como teste.
- **Verificado:** inserida.

---

## RD Station CRM

Regra que vale para quase todo o grupo: as ações atuam sobre a **Negociação mais recente** do lead. Mover, atualizar status e atualizar responsável **não criam** negociação: se ela não existir, a RD avisa que a ação não tem efeito (o levantamento não executou esse caso).

### Criar Negociação no CRM

- **O que faz:** cria uma negociação no RD Station CRM para o lead.
- **Campos:** funil; etapa; responsável, por **pessoa** (uma recebe todas) ou **equipe/grupo** (distribuição igual); opção "se o lead já foi negociação no CRM, atribuir ao último responsável".
- **Cuidados:** funil, etapa e equipe precisam existir no CRM. Com reentrada ligada, o mesmo lead pode gerar mais de uma negociação: decida isso de propósito.
- **Verificado:** configurada (funil, etapa e distribuição por equipe).

### Adicionar anotação

- **O que faz:** escreve uma anotação na negociação mais recente.
- **Campos:** texto com variáveis do Marketing (ex.: `*|NOME|*`), até **1024 caracteres**; botão "Quero uma sugestão" (IA da RD).
- **A RD avisa:** variável vazia no lead **não é inserida**.
- **Cuidados:** use para dar contexto ao vendedor (por que o lead chegou ali).
- **Verificado:** configurada. A sugestão por IA não foi testada.

### Criar tarefa na Negociação

- **O que faz:** cria uma tarefa na negociação criada durante o fluxo.
- **Campos:** assunto; tipo (Ligar, Email, Reunião, Tarefa, Almoço, Visita, WhatsApp); prazo com unidade (ex.: 24 horas); descrição; "não marcar aos fins de semana".
- **Cuidados:** a interface fala em negociação **criada durante o fluxo**: posicione depois de Criar Negociação.
- **Verificado:** configurada (Tarefa, 24 horas).

### Atualizar nome da Negociação

- **O que faz:** renomeia a negociação mais recente.
- **Campos:** texto com variáveis, até **128 caracteres**. Exemplo da própria RD: "Curso de inglês - `*|PRIMEIRO_NOME|*`, `*|ESTADO|*`".
- **A RD avisa:** variável vazia não é inserida no nome.
- **Verificado:** configurada.

### Atualizar tarefa

- **O que faz:** altera a tarefa mais recente da negociação mais recente.
- **Campos:** o que atualizar (assunto, tipo e/ou descrição) e o novo valor.
- **Verificado:** configurada (assunto).

### Adicionar produto à Negociação

- **O que faz:** adiciona um produto do CRM à negociação mais recente.
- **Campos:** produto; quantidade; valor; recorrência (ex.: Único); desconto opcional.
- **A RD avisa:** só funciona em negociação **em andamento**. Produtos são cadastrados em "Produtos e Serviços" nas configurações do CRM.
- **Verificado:** campos registrados; produto **não selecionado** (seleção pendente).

### Atualizar responsável

- **O que faz:** troca o responsável da negociação mais recente.
- **Campos:** uma pessoa.
- **A RD avisa:** todas as negociações que chegarem ali recebem o novo responsável, **independentemente do anterior**, exceto as que ainda não existem.
- **Cuidados:** é o responsável **da negociação** (CRM). O responsável **do lead** (Marketing) é outra ação.
- **Verificado:** bloco inserido; pessoa **não selecionada** (seleção pendente).

### Atualizar status

- **O que faz:** muda o status da negociação mais recente.
- **Campos:** Pausada, Perdida ou Vendida.
- **A RD avisa:** atualiza independentemente do status anterior; não afeta negociações que ainda não existem.
- **Verificado:** configurada (Pausada).

### Mover Negociação no CRM

- **O que faz:** move a negociação para outro funil e etapa.
- **Campos:** funil; etapa.
- **A RD avisa:** move independentemente do funil e etapa anteriores; não afeta negociações que ainda não existem.
- **Verificado:** configurada.

### Dividir caminho por produto

- **O que faz:** SIM/NÃO conforme a negociação mais recente tem um produto.
- **Campos:** um produto específico ou "Qualquer produto".
- **Verificado:** configurada ("Qualquer produto").

### Dividir caminho por qualificação

- **O que faz:** SIM/NÃO conforme a qualificação (estrelas) da negociação mais recente.
- **Campos:** valor **exato** de 1 a 5 (é "igual a", não "maior ou igual").
- **A RD avisa:** toda negociação nasce com qualificação **1**.
- **Cuidados:** para "4 ou mais", são necessárias duas divisões (4 e 5).
- **Verificado:** configurada (igual a 4).

### Dividir caminho por equipe

- **O que faz:** SIM/NÃO conforme a equipe associada à negociação mais recente.
- **Campos:** uma equipe ou grupo (cadastrados em "Convites, usuários e equipes" no CRM).
- **Verificado:** configurada.

---

## Integrações

### Enviar Leads para Integração

- **O que faz:** envia os dados do lead para uma URL (webhook) de outro sistema.
- **Campos:** URL de destino.
- **A RD avisa:** vão os campos padrão e personalizados, tags, estágio do funil, lead scoring, entre outros. Serve para notificações mobile, outros CRMs e sistemas parceiros.
- **Cuidados:** a URL precisa estar pronta para receber; teste com um endpoint controlado antes de apontar para produção. Os dados pessoais do lead saem da RD: o destino precisa ser autorizado por quem responde pela conta.
- **Verificado:** configurada com URL fictícia; **nenhum envio executado**.

---

## Responsável e Notificação

### Distribuir Leads entre os responsáveis

- **O que faz:** reparte os leads entre as pessoas selecionadas (fila).
- **Campos:** uma ou mais pessoas.
- **A RD avisa:** defina quem recebe **antes da marcação de oportunidade**, para o lead chegar ao CRM com dono. Lead sem responsável vai para o próximo da fila. Se já houver responsável e ele **não estiver na fila**, é **substituído**.
- **Cuidados:** a substituição pode tirar lead de quem já atendia. Revise a fila antes.
- **Verificado:** bloco inserido; pessoas **não selecionadas** (seleção pendente).

### Alterar responsável pelos Leads

- **O que faz:** define ou troca o responsável **do lead** no Marketing.
- **Campos:** uma pessoa.
- **Verificado:** bloco inserido; pessoa **não selecionada** (seleção pendente).

### Notificar responsável pelo Lead

- **O que faz:** envia email de aviso para a pessoa responsável pelo lead.
- **Campos:** nenhum.
- **A RD avisa:** o responsável é definido na Base de Leads (perfil do lead).
- **Cuidados:** sem responsável definido, não há para quem avisar. Coloque depois de Distribuir ou Alterar responsável.
- **Verificado:** inserida; nenhuma notificação enviada.

### Notificar email

- **O que faz:** envia aviso sobre o lead para um endereço fixo.
- **Campos:** um endereço de email.
- **A RD avisa:** a notificação leva os dados dos **campos padrão** do lead.
- **Cuidados:** pelo aviso da RD, só os campos padrão vão; campos personalizados provavelmente ficam de fora (não conferido). Se o time precisa deles, confira numa notificação de teste ou use Integração.
- **Verificado:** configurada com endereço fictício. Num fluxo de laboratório, executada com contato de teste: o aviso chegou à caixa indicada (30/09/2026).
