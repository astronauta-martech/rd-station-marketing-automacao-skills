# Modelo: régua de evento

Para eventos com data marcada: webinar, aula aberta, aula magna, live, workshop, evento presencial, feira. A régua cobre da inscrição ao pós-evento.

## Parâmetros que precisam ser respondidos

| Parâmetro | Por que importa |
|---|---|
| Data e hora do evento, e fuso | Define as esperas por data. O disparo usa o fuso configurado na conta. |
| Como a pessoa se inscreve, e o identificador da conversão | É a entrada. Com o conector, `landing_pages_search` e `forms_search` mostram o identificador. |
| A inscrição já está aberta? | Se já há inscritos, o recorte "Leads que já atendem aos critérios" inclui quem já se inscreveu. Se não, "vão atender". Antes de usar "já atendem", confirme que o identificador da conversão é exclusivo desta edição: se a página ou o formulário já foi usado em outro evento, o fluxo dispara para todo esse histórico. |
| Onde está o acesso | Link na confirmação ou só no lembrete? Evento presencial tem endereço e mapa. |
| Canal disponível | Só email, ou WhatsApp/SMS também (verificar se a conta permite). |
| Existe gravação? | Muda o pós-evento: quem faltou recebe a gravação. |
| Como saber quem participou | Segmentação de presentes, conversão de check-in, integração com a plataforma. Sem isso, o pós-evento não diferencia presentes e ausentes. |
| Próximo passo comercial | O que o evento quer gerar: oportunidade, matrícula, reunião, compra. |

## Estrutura base

Os ramos de uma espera por data **não se juntam sozinhos**: a estrutura abaixo mostra cada união com **Unir caminho** e dá rótulos aos trechos reaproveitados.

```
Entrada: conversão na inscrição
Reentrada: apenas uma vez

1. Adicionar Tags: evento-<aaaa-mm-dd>-<tema-curto>-inscrito
2. Enviar email: Confirmação (data, hora, acesso ou "o link chega no dia")
3. Esperar e agendar data e hora: <véspera>, 10:00
   3.ATÉ (inscritos antes da véspera):
     1. Enviar email: Lembrete da véspera
     2. Unir caminho → [L1]
   3.DEPOIS (inscritos a partir da véspera):
     1. [L1] Esperar e agendar data e hora: <dia do evento>, <1h antes>
        ATÉ:
          1. Enviar email: "Começa em 1 hora" (com acesso)
          2. Unir caminho → [P1]
        DEPOIS (inscritos com o evento começando ou já passado):
          1. [P1] Esperar e agendar data e hora: <dia seguinte>, 10:00
             ATÉ:
               1. [P2] <bloco de pós-evento, abaixo>
             DEPOIS (inscritos depois do pós-evento):
               1. Espera: 1 dia (para o pós não chegar junto com a confirmação)
               2. Unir caminho → [P2]

Bloco de pós-evento [P2], com lista de presença:
  Dividir caminho por segmentação: <presentes no evento>
   SIM: Adicionar Tags ...-presente > Enviar email: obrigado + material + próximo passo
        > (opcional) Marcar Oportunidade, se o evento qualifica
   NÃO: Adicionar Tags ...-ausente > Enviar email: gravação ou convite pro próximo

Bloco de pós-evento [P2], sem lista de presença:
  Enviar email único, com texto neutro ("obrigado pela inscrição", sem supor
  que a pessoa assistiu) + gravação + próximo passo
```

Quem se inscreve em cada momento:

| Inscrição | Recebe |
|---|---|
| Antes da véspera | Confirmação, véspera, 1 hora antes, pós-evento |
| Entre a véspera e 1 hora antes | Confirmação, 1 hora antes, pós-evento |
| Com o evento começando ou passado | Confirmação (com acesso ou gravação), pós-evento |
| Depois do pós-evento | Confirmação e, 1 dia depois, o pós-evento |

Decida também o que acontece com a landing page depois do evento: sair do ar ou virar página da gravação.

## Variações

- **Evento presencial:** trocar "acesso" por endereço, horário de credenciamento e mapa; lembrete da véspera com o que levar.
- **Sem lista de presentes:** o pós-evento vira um email único para todos, com gravação e próximo passo. Deixe claro na especificação que a divisão não é possível.
- **Inscrição paga:** a entrada é a confirmação de pagamento (conversão de venda ou integração), não o formulário.
- **Série de aulas:** repetir o bloco de lembrete por aula, com uma espera por data para cada uma, e unir caminhos quando a pessoa entra no meio da série.
- **Com WhatsApp:** o lembrete de 1h pode ir por WhatsApp, se o público usa esse canal. Só use se a ação estiver disponível na conta.

## Armadilhas

1. **Esquecer o ramo "depois do dia".** Toda espera por data cria dois caminhos. Inscrito tardio cai no DEPOIS e, sem ação ali, não recebe nada.
2. **Fuso da conta errado.** Confira em Configurações > Visão Geral antes de marcar horário.
3. **Reentrada ligada** em evento: quem se inscreve duas vezes recebe tudo em dobro.
4. **Datas passadas:** o calendário da RD não aceita data passada. Se o fluxo for reaproveitado para o próximo evento, as datas precisam ser trocadas uma a uma.
5. **Pós-evento sem critério de presença:** dividir por uma segmentação que não existe ou está desatualizada manda o email errado pra metade das pessoas.

## Métricas

- Taxa de abertura da confirmação (sinal aproximado de entrega: a abertura é estimada e pode vir distorcida por bloqueio de imagem ou proteção de privacidade do leitor de email).
- Taxa de abertura do lembrete de 1h.
- Presentes ÷ inscritos (show rate), se houver lista de presença.
- Oportunidades ou vendas marcadas pelo fluxo (Opções > Estatísticas).
