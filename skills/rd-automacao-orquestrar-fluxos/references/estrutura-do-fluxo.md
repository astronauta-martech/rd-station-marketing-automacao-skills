# Estrutura de um fluxo de automação no RD Station Marketing

Complemento do catálogo de ações: o que envolve as ações (entrada, configurações, saída, salvamento) e as regras de desenho que valem para o fluxo inteiro. Levantado em setembro de 2026 na interface da RD, lendo fluxos existentes e criando um fluxo de teste inativo.

## Anatomia do editor

O topo do editor tem três entradas:

- **Editor:** o desenho (entrada, ações, divisões).
- **Configurações:** finais de semana nas esperas e reentrada.
- **Saída:** quando o lead deixa o fluxo antes do fim.

Configurações e Saída abrem como **painéis laterais** sobre o desenho e fecham no **X**.

Botões: **Salvar** (grava sem ativar), **Salvar e Ativar** (grava e liga), download do fluxo e "Sair do editor". Novas ações entram pelo botão **Ações** ou pelos conectores **+** entre os blocos.

## Criar um fluxo

**Automação de Marketing > Criar fluxo** abre uma galeria de **Modelos de Automação** (categorias API, Boas Vindas, RD Tracker e lojas virtuais como Loja Integrada, Nuvemshop, Shopify, Tray, VTEX, Wix e WooCommerce). Para começar do zero: **Criar fluxo em branco**. O editor pede o nome do fluxo antes de tudo.

## Entrada

O bloco inicial diz quais leads "devem percorrer o fluxo". Em **Selecionar uma entrada**, primeiro se escolhe o recorte, depois o tipo.

**Recorte** (texto da própria RD):
- **Leads que vão atender aos critérios:** ao ativar o fluxo, entram leads que vão atender às condições **após a ativação**.
- **Leads que já atendem aos critérios:** ao ativar, entram os leads que **já atendem** às condições **e** os que vão atender no futuro. Cuidado: pode disparar o fluxo para todo o histórico de uma vez.

**Tipos de entrada** (9, na ordem do menu):

| Tipo | Descrição na RD | Observações |
|---|---|---|
| Converteram no evento | Landing Pages, forms, pop-ups e APIs | O mais comum. Aponta um identificador de conversão. |
| Entraram na lista de segmentação | Lista de Leads criadas em Segmentação | Para entrar por tag, crie uma segmentação com a tag. |
| Foram marcados como oportunidade | Leads marcados no RD Station Marketing | |
| Campo do Lead | Dados e características de Leads | Ex.: "campo X está preenchido", combinável com "ou". |
| Eventos CRM | Negociação, venda, tarefa, produto... | Subtipos: Negociação criada, Negociação perdida, Negociação atualizada, Venda, Tarefa criada, Tarefa atualizada. |
| Evento de Integração | Chat, Mídia e WhatsApp | |
| Evento de ecommerce | Evento de Integração ecommerce | |
| Atividades no ecommerce | Atividades no seu ecommerce | |
| Potencial de compra | Faixa de potencial de compra do lead | |

Uma entrada pode ter mais de uma condição (**Criar outra condição**).

**"Definir entrada depois"** não deixa o fluxo sem entrada: ele passa a aceitar leads **adicionados a partir de outro fluxo ou inseridos manualmente**, inclusive pelo conector, com `workflow_lead_enroll` (testado num fluxo de laboratório com contato de teste, em 30/09/2026). A ferramenta só aceita fluxo **ativo**. Inscrever um lead dispara o fluxo na hora (emails, estágio, CRM, notificações): só faça com confirmação explícita de quem responde pela conta, dizendo qual fluxo e qual contato.

## Configurações

- **Considerar finais de semana nas ações de Espera** (ligado nos fluxos vistos). Desligado, o texto da RD diz que os leads esperam até segunda-feira para seguir. O esperado é que isso afete só a espera que terminaria no sábado ou no domingo (leitura da interface; não medido).
- **Um mesmo lead pode percorrer este fluxo:**
  - **Mais de uma vez:** percorre de novo sempre que voltar a preencher a entrada (converter de novo, sair e entrar na lista).
  - **Apenas uma vez:** é ignorado nas próximas vezes.

Nos seis fluxos analisados, todos estavam com reentrada ligada, finais de semana considerados e só a saída padrão, e um fluxo criado em branco no levantamento abriu da mesma forma. Tudo indica que é o padrão da ferramenta (confira na tela ao criar), não necessariamente o certo para cada objetivo.

## Saída

"Um lead poderá sair deste fluxo quando atender qualquer uma das condições":

- **Ao chegar ao final do fluxo** (sempre marcada, não dá para desligar);
- **Ao receber marcação Oportunidade**;
- **Ao registrar qualquer uma das conversões** (escolhidas);
- **Ao registrar qualquer um dos eventos de integração**;
- **Ao registrar qualquer um dos eventos de ecommerce**.

Ao sair, o lead não recebe as próximas ações do caminho. É o recurso para parar a nutrição de quem já comprou ou já virou oportunidade. A saída **não aceita segmentação** nem tag: para excluir um público pela lista, use **Dividir caminho por segmentação** logo no início.

Cuidado com a saída por conversão em fluxos que avisam alguém: se o lead converte durante uma espera, sai antes do aviso.

## Salvar e ativar

- O fluxo **não salva sem pelo menos uma ação**: a validação bloqueia.
- Ao salvar, a RD mostra uma revisão com reentrada, finais de semana e saída, e pede confirmação.
- **Salvar** deixa o fluxo **INATIVO** na listagem. Só **Salvar e Ativar** liga.
- Depois de salvo, o fluxo ganha uma URL própria no editor (`.../editor/<id>`); o mesmo id é o que o conector usa.

## Menu de cada fluxo na listagem

O botão de opções (três pontos) de cada fluxo tem: Editar, Desativar (ou Ativar), Duplicar, Estatísticas, **Inserir Leads no fluxo** (inscrição manual pela tela) e Excluir fluxo. Ao desativar, a RD avisa que os leads que ainda estiverem percorrendo o fluxo saem dele. Ativar, Desativar, Inserir Leads e Excluir afetam leads reais: só com confirmação explícita de quem responde pela conta.

## Métricas na listagem

- **Entrada de leads:** quantas entradas o fluxo recebeu. Com reentrada, **não** é o número de pessoas únicas.
- **Leads ativos:** quantos estão percorrendo agora.
- **Opções > Estatísticas:** qualificações, oportunidades e vendas registradas pelo fluxo.

Volume não mede complexidade nem qualidade: um fluxo de uma única ação pode ter dezenas de milhares de entradas.

## Regras de desenho que valem para o fluxo inteiro

1. **Espera antes de condição de email.** Entre Enviar email e Dividir caminho por email do fluxo, sempre uma espera (Espera, Esperar e agendar hora ou Esperar e agendar data e hora). Pode haver outros blocos no meio; o que importa é existir tempo para abrir.
2. **A condição de email só enxerga emails do próprio fluxo.**
3. **Responsável antes da oportunidade.** Distribua ou defina o responsável antes de Marcar Oportunidade quando o lead precisa chegar ao CRM com dono.
4. **CRM age na negociação mais recente.** Mover, atualizar status e atualizar responsável não criam negociação. Produto só entra em negociação em andamento.
5. **Qualificação é valor exato.** "4 ou mais" exige duas divisões.
6. **Encadear fluxos ignora a entrada do destino.** Adicionar a outro fluxo exige destino ativo e faz o lead percorrer de novo.
7. **Remover de outros fluxos vale até o fim do caminho atual** (segundo o painel da RD; não testado com lead).
8. **Variável vazia some.** Em anotação e nome de negociação, campo vazio não é inserido: escreva textos que façam sentido sem ele.
9. **Marcar Venda não confere compra.** Só depois de evidência.
10. **Espera por data sempre tem o ramo "depois do dia".**
11. **Reentrada + Criar Negociação** pode gerar negociações repetidas para o mesmo lead: decidir de propósito e registrar a decisão.
12. **Saída é parte do desenho.** Quem já converteu no objetivo deve sair; configure o painel Saída, não só o caminho.
13. **Marcar Oportunidade + saída por oportunidade tende a cortar o resto.** Com "Ao receber marcação Oportunidade" ligada, o lead deve sair do fluxo ao ser marcado, e as ações seguintes não acontecem (dedução pelo painel Saída; não testado com lead). Desligue essa saída ou deixe a marcação por último.
14. **Ramos não se juntam sozinhos.** Depois de uma divisão, tudo fica dentro de cada ramo; para voltar a um caminho comum, use **Unir caminho**. Três ou mais caminhos exigem divisões encadeadas.
15. **Editar fluxo ativo: conte que vale só daqui para a frente** (comportamento esperado, não testado no levantamento). Quem já passou do ponto editado não deve voltar para percorrer os blocos novos. Dois fluxos ativos com a mesma entrada fazem o lead percorrer os dois (envio em dobro, se mandam o mesmo). Ao redesenhar, decida entre editar o atual ou criar outro e desativar o antigo, sempre com confirmação de quem responde pela conta: desativar tira do fluxo quem ainda está nele.

## Método que funcionou para montar no editor

Útil para quem monta (pessoa ou agente no navegador). Criar ou salvar um fluxo, mesmo inativo, é escrita na conta: só com autorização explícita de quem responde por ela. Skills que só leem ou desenham não montam.

1. Abrir o conector **+**, expandir a categoria e **arrastar** a ação até o conector. Se o arraste pelo centro do cartão não pegar, usar o controle na borda direita.
2. **Esperar a interface estabilizar** antes de preencher: o editor tem animações e atualizações assíncronas.
3. Preencher e **conferir o texto no bloco antes de fechar o painel**. Fechar logo após digitar já manteve o valor anterior.
4. A cada inserção o canvas se reorganiza: posições anteriores deixam de valer. Localizar de novo antes do próximo arraste.
5. Rolagem horizontal e zoom reduzido ajudam em fluxos longos.
6. Nunca usar **Salvar e Ativar** em teste. Nomear rascunhos de forma inconfundível (ex.: `[RASCUNHO] ... | NAO ATIVAR`).
