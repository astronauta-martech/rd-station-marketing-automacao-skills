# Changelog

Mudanças relevantes deste projeto. O formato segue o [Keep a Changelog](https://keepachangelog.com/pt-BR/1.1.0/) e a numeração segue o [Versionamento Semântico](https://semver.org/lang/pt-BR/).

## [0.1.0] - 2026-09-30

Primeira versão, preparada e validada ao vivo em 30/09/2026.

### Adicionado

- **12 skills de automação do RD Station Marketing:**
  - `rd-automacao-catalogo`: explica as 38 ações do editor, com campos, avisos e ordem certa.
  - `rd-automacao-briefing`: transforma um objetivo em especificação de fluxo revisável.
  - `rd-automacao-regua-de-evento`: régua de inscrição, confirmação, lembretes e pós-evento.
  - `rd-automacao-recuperacao-e-nutricao`: recuperação de quem não abriu ou não clicou, saída de quem comprou e nutrição até o próximo passo.
  - `rd-automacao-passagem-para-crm`: passagem do Marketing para o CRM, com responsável, oportunidade, negociação e tarefa.
  - `rd-automacao-raio-x-da-conta`: inventário e higiene de todos os fluxos de uma conta, com prioridades.
  - `rd-automacao-diagnostico-de-fluxo`: estrutura, entradas, onde os leads param e emails de um fluxo.
  - `rd-automacao-orquestrar-fluxos`: sobreposição de leads entre fluxos e regras de prioridade.
  - `rd-automacao-treinamento`: aula, desafios e quiz com gabarito para formar quem opera contas.
  - `rd-automacao-montar`: monta a especificação no editor da RD pelo navegador e salva sempre inativo.
  - `rd-automacao-conferir`: confere o fluxo montado contra a especificação antes de ativar, sem alterar nada.
  - `rd-automacao-testar-com-lead`: inscreve um contato de teste pelo conector, com confirmação explícita, e prova o percurso.
- **Base de conhecimento em `base/`**, fonte única copiada para dentro de cada skill:
  - catálogo das 38 ações em 7 categorias (34 configuradas no levantamento e 4 que dependem do plano da conta);
  - estrutura do fluxo: entrada, configurações, saída e regras de desenho;
  - formato de especificação de fluxo;
  - três modelos: régua de evento, recuperação e nutrição, passagem para o CRM;
  - conector MCP do RD Station Marketing: ferramentas, o que ele não mostra e limites de chamadas observados;
  - tradução dos códigos de ação devolvidos pelo conector;
  - disciplina de evidência: fonte de cada número, níveis de certeza e cuidados em recomendações que mexem em fluxo;
  - `mapa.json`, que diz quais arquivos de base cada skill leva.
- `scripts/sincronizar.py`, que copia `base/` e `LICENSE` para dentro de cada skill e confere as cópias com `--check`.
- Marketplace do Claude Code em `.claude-plugin/marketplace.json`, com o plugin `rd-station-marketing-automacao`.
- Licença de Uso Astronauta Martech 1.0, em português e inglês.
- README, guia de contribuição e `.gitignore`.

### Validação

- O catálogo nasceu de um levantamento na interface real da RD, ação por ação, em setembro de 2026.
- As 12 skills foram executadas ao vivo numa conta real do RD Station Marketing antes da publicação. Os exemplos dos `SKILL.md` seguem o formato dessas execuções, com nomes anonimizados ou fictícios e números alterados.
- `rd-automacao-raio-x-da-conta` e `rd-automacao-orquestrar-fluxos` passaram também por verificação independente do resultado contra os dados da conta.
- `rd-automacao-montar` e `rd-automacao-conferir` foram validadas num fluxo de laboratório, montado e salvo inativo.
- `rd-automacao-testar-com-lead` foi validada de ponta a ponta nesse fluxo de laboratório, com contato de teste e conferência do efeito na conta.
- Os limites de chamadas do conector encontrados nas execuções estão documentados em `base/conector-rd-marketing.md`, e as skills de diagnóstico foram ajustadas para eles.
