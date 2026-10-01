# Skills de Automação para o RD Station Marketing

Skills para **Claude Code** e **Codex** trabalharem na **Automação de Marketing** do RD Station Marketing: desenhar, montar, conferir, testar e diagnosticar fluxos, a partir de pedidos em português. Feitas pela [Astronauta Martech](https://astronauta.digital), Parceiro Diamante Exclusivo RD Station 2026.

Tudo nasceu de um levantamento feito na interface real da RD, ação por ação, em setembro de 2026. Cada skill foi executada ao vivo numa conta real antes de entrar aqui, e durante o período de testes as skills foram usadas em mais de 30 contas do RD Station Marketing.

Página do pacote: [astronauta.digital/materiais/skills/rd-station-marketing-automacao](https://astronauta.digital/materiais/skills/rd-station-marketing-automacao)

## Instalação

### Claude Code e Codex, com um comando

```bash
npx skills add astronauta-martech/rd-station-marketing-automacao-skills -g
```

O instalador pergunta quais skills você quer e em qual ferramenta instalar. O `-g` instala na sua pasta de usuário, e as skills valem em qualquer projeto; sem ele, ficam só no projeto da pasta atual. Para escolher direto:

```bash
npx skills add astronauta-martech/rd-station-marketing-automacao-skills -g --skill rd-automacao-catalogo --agent claude-code codex
```

### Claude Code, como plugin

Dentro do Claude Code:

```
/plugin marketplace add astronauta-martech/rd-station-marketing-automacao-skills
/plugin install rd-station-marketing-automacao@astronauta-martech
```

### Manual

Clone o repositório e copie a pasta da skill que quiser:

```bash
git clone https://github.com/astronauta-martech/rd-station-marketing-automacao-skills.git
mkdir -p ~/.claude/skills
cp -R rd-station-marketing-automacao-skills/skills/rd-automacao-catalogo ~/.claude/skills/
```

No Codex, o destino é `~/.agents/skills/`, a pasta de usuário documentada pelo Codex (versões antigas liam `~/.codex/skills/`). Para a skill valer só num projeto, copie para `.claude/skills/` (Claude Code) ou `.agents/skills/` (Codex) na raiz dele.

## As skills

| Skill | O que faz | Onde age | Validação |
|---|---|---|---|
| `rd-automacao-catalogo` | Explica cada uma das 38 ações do editor, campos, avisos e ordem certa | só consulta | validada ao vivo |
| `rd-automacao-briefing` | Transforma um objetivo em especificação de fluxo revisável | só consulta (conector opcional) | validada ao vivo |
| `rd-automacao-regua-de-evento` | Régua de inscrição, confirmação, lembretes e pós-evento | só consulta (conector opcional) | validada ao vivo |
| `rd-automacao-recuperacao-e-nutricao` | Quem não abriu, não clicou ou já comprou; nutrição até o próximo passo | só consulta (conector opcional) | validada ao vivo |
| `rd-automacao-passagem-para-crm` | Do Marketing ao CRM: responsável, oportunidade, negociação, tarefa | só consulta (conectores opcionais) | validada ao vivo |
| `rd-automacao-raio-x-da-conta` | Inventário e higiene de todos os fluxos da conta, com prioridades | conector RD + navegador opcional | validada ao vivo + verificação independente |
| `rd-automacao-diagnostico-de-fluxo` | Estrutura, entradas, onde os leads param e emails de um fluxo | conector RD | validada ao vivo |
| `rd-automacao-orquestrar-fluxos` | Sobreposição de leads entre fluxos e regras de prioridade | conector RD + navegador opcional | validada ao vivo + verificação independente |
| `rd-automacao-treinamento` | Aula, desafios e quiz com gabarito para formar quem opera contas | só consulta | validada ao vivo |
| `rd-automacao-montar` | Monta a especificação no editor da RD, sempre inativa | navegador | validada ao vivo |
| `rd-automacao-conferir` | Confere o fluxo montado contra a especificação antes de ativar | navegador + conector RD | validada ao vivo |
| `rd-automacao-testar-com-lead` | Inscreve um contato de teste e prova o percurso | conector RD | validada ao vivo (ponta a ponta) |

**Onde age:** "só consulta" não precisa de acesso à conta; "conector RD" usa o conector MCP do RD Station Marketing; "navegador" opera o editor da RD pelo navegador conectado.

## Requisitos

- **Só consulta** (catálogo, briefing, régua de evento, recuperação e nutrição, passagem para o CRM, treinamento): basta o Claude Code ou o Codex. Com o conector ligado, briefing, régua, recuperação e passagem conferem na conta o que já existe (conversões, fluxos, emails).
- **Conector RD** (raio-x, diagnóstico, orquestrar, conferir, testar com lead): o conector MCP oficial do RD Station Marketing ligado no seu cliente. Como conectar: [rdstation.com/inteligencia-artificial/mcp](https://www.rdstation.com/inteligencia-artificial/mcp/); catálogo de ferramentas em [mcp.rdstationmentor.com](https://mcp.rdstationmentor.com/). As métricas de email por fluxo exigem plano Pro ou superior.
- **Conector do RD Station CRM** (opcional na passagem para o CRM): confere funis, etapas, equipes e produtos antes de desenhar a passagem.
- **Navegador** (montar e conferir; opcional no raio-x e no orquestrar): um navegador que o agente controle, com a sessão do RD Station Marketing aberta.
- **Escrita na conta:** só duas skills alteram a conta. `rd-automacao-testar-com-lead` inscreve um contato pelo conector, e só depois de um "sim" explícito. `rd-automacao-montar` cria o fluxo pelo navegador e salva sempre inativo, para uma pessoa revisar e ativar. As demais só leem.

## Estrutura

```
base/                  fonte única do conhecimento (catálogo, estrutura do fluxo,
                       modelos, conector, códigos de ação, disciplina de evidência)
  mapa.json            quais arquivos de base cada skill leva
skills/<nome>/         uma pasta por skill, autossuficiente
  SKILL.md
  references/          cópia gerada de base/
  LICENSE              cópia gerada
scripts/sincronizar.py copia base/ e LICENSE para dentro das skills
.claude-plugin/        manifesto do marketplace do Claude Code
```

Edite sempre em `base/` e rode `python3 scripts/sincronizar.py`. Nunca edite `references/` direto: é sobrescrito. Para contribuir, veja [CONTRIBUTING.md](CONTRIBUTING.md); o histórico de versões está em [CHANGELOG.md](CHANGELOG.md).

## Limites conhecidos do conector

O conector MCP do RD Station Marketing aceita bem menos chamadas do que anuncia nas ferramentas de fluxo e de leads (na prática, de 10 a 20 chamadas por meia hora em cada ferramenta de leads e até 10 por hora no detalhe de fluxo, com bloqueio de 30 minutos ou mais). As skills de diagnóstico foram desenhadas para isso: usam a listagem da RD no navegador quando disponível e detalham poucos fluxos por vez. Detalhes em `base/conector-rd-marketing.md`.

## Licença

Resumo: **pode usar, adaptar, compartilhar de graça e cobrar pelo trabalho que fizer com estas skills, inclusive para clientes e inclusive sendo concorrente. Não pode cobrar pelas skills em si** (revender, sublicenciar ou incluí-las em produto pago, como curso, pacote de templates, assinatura, marketplace ou loja de plugins). O que as skills produzem no seu uso (especificações, relatórios, fluxos montados, materiais de treinamento) pode ser usado livremente, inclusive de forma comercial, desde que não reproduza parte substancial das skills. Toda cópia ou adaptação mantém o aviso de licença e o crédito à Astronauta Martech.

Texto completo em [LICENSE](LICENSE). Não é uma licença de código aberto no sentido da OSI: o conteúdo é aberto para ler, usar e adaptar, mas não para vender. Copyright © 2026 Astronauta Digital LTDA.

RD Station, Claude e Codex são marcas de seus respectivos titulares. Este projeto não é afiliado nem endossado por eles.
