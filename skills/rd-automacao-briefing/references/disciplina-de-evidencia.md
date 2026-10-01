# Disciplina de evidência

Regras para toda skill que lê uma conta real (conector ou navegador) e tira conclusões dela. Elas existem porque os testes mostraram o mesmo erro repetido: indício escrito como fato.

## 1. Todo número tem fonte

Marque a origem de cada contagem, uma vez por seção ou entre parênteses:
- **(listagem, lida às HH:MM)**: tela Automação de Marketing. "Entrada de leads" é o total desde a criação; "Leads ativos" é um retrato do momento da leitura; "-" foi lido como 0 (declare).
- **(conector)**: dado das ferramentas do conector; listas de leads por fluxo atualizam uma vez por dia.
- **(medido: DD/MM a DD/MM)**: contagem feita no período.
- **(calculado)**: diga a regra (ex.: "teto = menor valor de Leads ativos do par").

## 2. Três níveis de certeza, sempre escritos

| Nível | Quando | Como escrever |
|---|---|---|
| Confirmado | Veio de campo do conector, da tela ou de medição | afirmação direta |
| Provável | Dedução de dados (ex.: mesmo código de ação e mesma contagem) | "provável", "possível", com "?" no rótulo |
| Pelo nome | Só o nome do fluxo sugere | "(pelo nome)" |

Exemplos:
- Mesmo código de ação em dois fluxos é **"mesmo tipo de ação"**, não "mesma ação". Duplicado sem "?" só quando entrada e destino foram comparados.
- "Envio em dobro" ou "leads em comum" só com a interseção medida. Sem medição: "possível envio em dobro", com as hipóteses.
- Tipo de fluxo ("de emails", "de nutrição") sem detalhe: "(pelo nome)".
- `updated_at` é **última edição**, nunca "data de desativação". "Nunca ativado" não se conclui de zero entradas.
- Tudo o que vem de Leads ativos se escreve "no instante da leitura (HH:MM)". "Leads ativos = 0" quer dizer "ninguém percorrendo naquela hora", não "não há conflito".
- Interseção de listas de entrada é "entraram nos dois no período", não "estão nos dois agora".

## 3. Separe "fora do plano", "não consultado" e "não exposto"

- **Fora do plano (escolha):** ficou de fora da seleção da skill.
- **Não consultado (limite do conector):** estava no plano, mas o 429 ou o custo impediu.
- **Não exposto pelo conector:** entrada, configuração das ações, ramos, destino de integração, fluxo de destino de "Adicionar Leads a outros fluxos".

Use a mesma formulação em todas as seções.

## 4. Conhecimento da plataforma tem fonte

Comportamento da RD (ex.: "Remover Lead de outros fluxos vale até o fim do caminho atual") vem das referências da skill e deve ser apresentado assim ("segundo a documentação da skill"), separado do que foi observado na conta.

## 5. Recomendações que mexem em fluxo

Toda recomendação de **desativar, unificar, substituir, remover leads ou mudar entrada** traz:
1. o que foi conferido e o que falta conferir no editor, sempre os quatro itens: **entrada, destino, reentrada, responsável** (se um não se aplica, diga por quê);
2. quantos leads estão percorrendo e a transição de cada fluxo que sai: bloquear novas entradas, deixar quem está terminar, desativar só quando Leads ativos chegar a zero;
3. num par, **qual manter e por quê**, e o impacto se o desligado for o errado (uma regra que agrupa vários pares cumpre isso para cada par);
4. o fecho "confirme antes de ...".

Regra proposta para fluxo **não detalhado** é sempre condicional ("se, no editor, X, então Y").

## 6. Limite do conector (429)

Leia o `cooldown_seconds` que o 429 devolveu. Se for até 120 segundos, espere esse tempo e repita **a mesma chamada uma única vez**. Se voltar 429, pare aquela ferramenta e siga com o que tem. Cite o valor devolvido, não um número de referência. Nos testes, um 429 numa ferramenta não impediu usar as outras (as cotas parecem separadas por ferramenta): tente as demais antes de concluir que o conector inteiro parou.

## 7. Listas geradas por critério

Quando listar fluxos por critério (data, nome, contagem), aplique o critério a **todos** os fluxos e liste todos os que batem. Se mostrar só alguns, escreva "entre outros" e o total.

## 8. Revisão final de palavras

Antes de entregar, procure "estão em", "estão no", "estão nos", "ação igual", "idênticos", "duplicado" (sem "?") e "desativado em" em frases sobre dados medidos ou deduzidos, e troque pela forma do nível de certeza certo.

## 9. Seções obrigatórias no fim

- **O que assumi:** período, critérios de seleção, leituras como "- = 0", interpretações de nome.
- **Limites e o que não foi visto:** o que ficou sem consultar e o que o conector não expõe, com o próximo passo para fechar.
