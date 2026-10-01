# Modelo: recuperação e nutrição

Para trazer de volta quem não reagiu e para conduzir quem demonstrou interesse até o próximo passo. Cobre quatro situações que se repetem em quase toda conta.

## Parâmetros que precisam ser respondidos

| Parâmetro | Por que importa |
|---|---|
| Qual é o próximo passo desejado | Compra, matrícula, reunião, resposta. Define o que é "converter" e a saída do fluxo. |
| O que já foi enviado | A recuperação se refere a um email específico do próprio fluxo. |
| Quem já converteu | Precisa de uma segmentação ou conversão que identifique quem já fez o próximo passo. |
| Quantos toques cabem | Cada envio sem reação aumenta o risco de descadastro; o padrão destas skills é no máximo 3. |
| Janela de envio | Horário comercial? Esperar e agendar hora resolve. |

## 1. Não abriu

```
1. Enviar email: <principal>
2. Espera: 2 dias
3. Dividir caminho por email do fluxo: Abriu <principal>
   SIM: (segue a nutrição ou termina)
   NÃO:
     1. Enviar email: <recuperação> (assunto novo, mesmo conteúdo ou versão curta)
```

Regra: a condição só enxerga emails **do próprio fluxo**. A espera antes da condição é obrigatória.

## 2. Abriu mas não clicou

Este bloco entra no **3.SIM** da situação 1 (quem abriu). Usado sozinho, logo depois do envio, o NÃO mistura quem não abriu com quem abriu e não clicou.

```
3.SIM.1. Dividir caminho por email do fluxo: Clicou em <principal>
   SIM: 1. Adicionar Tags: <interesse-x>   2. (próximo passo)
   NÃO:
     1. Esperar e agendar hora: 1 dia, entre 10:00 e 12:00
     2. Enviar email: <reforço do benefício, CTA único>
```

## 3. Já comprou: tirar da nutrição

Duas formas, que podem ser combinadas:

- **Saída do fluxo** (painel Saída): "Ao registrar qualquer uma das conversões" com a conversão de compra, ou "Ao receber marcação Oportunidade". Tira o lead de qualquer ponto do percurso.
- **Divisão no caminho:** antes de cada envio comercial, **Dividir caminho por segmentação** com a lista de compradores. SIM: **Alterar estágio dos Leads** para Cliente, **Adicionar Tags** `cliente-<produto>`, **Remover Tag** `interesse-<produto>`, **Marcar Venda** se a venda não foi registrada por outro caminho.

A saída é mais robusta (vale em qualquer ponto). A divisão permite fazer algo com quem comprou.

## 4. Nutrição por interesse

```
Entrada: conversão em material, ou segmentação com a tag de interesse
Reentrada: apenas uma vez
Saída: ao registrar a conversão de compra ou pedido de contato

1. Enviar email: conteúdo 1 (entrega do que a pessoa pediu)
2. Esperar e agendar hora: 2 dias, 10:00 a 12:00
3. Enviar email: conteúdo 2 (aprofunda o problema)
4. Esperar e agendar hora: 3 dias, 10:00 a 12:00
5. Dividir caminho por email do fluxo: Clicou em conteúdo 2
   SIM:
     1. Enviar email: convite pro próximo passo
     2. (opcional, só se o comercial aceitar clique como sinal de interesse)
        Distribuir Leads entre os responsáveis > Marcar Oportunidade
        (Distribuir substitui responsável fora da fila e a marcação pode levar
        o lead ao CRM: registre em Decisões em aberto)
   NÃO:
     1. Enviar email: conteúdo 3 (caso de sucesso)
```

## Casos especiais

- **Recuperação em fluxo que já existe:** a condição só enxerga emails do próprio fluxo, então a recuperação entra **no fluxo atual** (edição), não num fluxo novo. Quem já passou do ponto editado não recebe a recuperação (regra 15).
- **Email de confirmação (double opt-in):** se o email principal pede para a pessoa clicar e confirmar, o sinal certo é **Clicou** (ou a conversão de confirmação), não **Abriu**.
- **Volume baixo:** com poucos inscritos por mês, nenhuma métrica vai mostrar efeito tão cedo. Diga isso e sugira atacar a entrada antes da recuperação.

## Armadilhas

1. **Recuperação que manda o mesmo email com o mesmo assunto.** Mude o assunto e o primeiro parágrafo: o assunto é o que a pessoa vê antes de decidir abrir.
2. **Nutrição sem saída.** Quem compra no meio continua recebendo oferta. Configure o painel Saída.
3. **Marcar Venda por suposição.** O bloco não confere compra.
4. **Tags que só acumulam.** Se adiciona `interesse-x`, defina onde ela é removida.
5. **Reentrada ligada** em nutrição: o lead que converte de novo no material recomeça a sequência do zero.

## Métricas

- Abertura e clique por email do fluxo (métricas de email por fluxo, no conector ou na tela do fluxo).
- Quantos chegam ao ramo NÃO da recuperação e quantos desses abrem a recuperação.
- Conversões no próximo passo e saídas pelo painel Saída.
