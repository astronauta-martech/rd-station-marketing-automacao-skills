# Códigos de ação do conector

`workflow_get_details` devolve cada ação do fluxo como `{id, type}`, onde `type` é um código de 5 letras. Esta tabela traduz o código para o nome da ação no editor.

Como foi levantada (setembro de 2026): cruzando os ícones internos do editor (`dynamic-action-<código>`), os fluxos de uma conta real e um fluxo montado de propósito com ações conhecidas (esse fluxo devolveu `ADTAG, WTDEL, CDSEG, ADTAG, NTEMA, ADTAG`, confirmando `CDSEG` e `NTEMA`).

| Código | Ação no editor | Confiança |
|---|---|---|
| `SDEMA` | Enviar email | certo |
| `SDSMS` | Enviar SMS | provável |
| `WTDEL` | Espera | certo |
| `WTFIX` | Esperar e agendar hora (ou Esperar e agendar data e hora) | provável |
| `CDSEG` | Dividir caminho por segmentação | certo |
| `ADLTW` | Adicionar Leads a outros fluxos | certo |
| `ADTAG` | Adicionar Tags | certo |
| `RMTAG` | Remover Tag | certo |
| `MKSAL` | Marcar Venda | certo |
| `MKOPP` | Marcar Oportunidade | certo |
| `UNOPP` | Desmarcar Oportunidade | certo |
| `CGLST` | Alterar estágio dos Leads | provável |
| `ASLLB` | Adicionar Base Legal | provável |
| `CRMOP` | Criar Negociação no CRM | provável |
| `CRMUT` | Atualizar tarefa | certo |
| `SDPWH` | Enviar Leads para Integração | certo |
| `OWROU` | Distribuir Leads entre os responsáveis | certo |
| `NTOWN` | Notificar responsável pelo Lead | certo |
| `NTEMA` | Notificar email | certo |

Códigos ainda não observados: Enviar WhatsApp, Enviar Mensagem Inteligente, Teste A/B, Remover Lead de outros fluxos (qualquer código fora da tabela pode ser ele: liste para conferir), Dividir caminho por email do fluxo, Unir caminho, Remover Base Legal, Adicionar anotação, Criar tarefa na Negociação, Atualizar nome da Negociação, Adicionar produto à Negociação, Atualizar responsável, Atualizar status, Mover Negociação no CRM, Dividir caminho por produto, qualificação e equipe, Alterar responsável pelos Leads. Código fora da tabela aparece no resultado como está, marcado "(código não mapeado)".

## Como ler a lista de ações

- **A ordem devolvida não é a ordem do percurso.** Já foi visto fluxo terminando numa espera e negociação criada "antes" da oportunidade. Não conclua ordem nem regra de desenho a partir da sequência do conector.
- **Não há ramos.** Divisões e seus caminhos vêm achatados numa lista só.
- **Não há configuração.** O código diz qual ação é, não qual email, tag, espera ou funil.
- **Grupos úteis para análise:**
  - envia algo para o lead: `SDEMA`, `SDSMS` (e o de WhatsApp, quando mapeado);
  - envia para fora: `SDPWH`, `NTOWN`, `NTEMA`;
  - mexe no CRM: `CRMOP`, `CRMUT` (e demais de CRM);
  - marca o lead: `ADTAG`, `RMTAG`, `MKOPP`, `UNOPP`, `MKSAL`, `CGLST`, `ASLLB`;
  - liga fluxos: `ADLTW`.
