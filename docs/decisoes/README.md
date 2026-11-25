# Registro de decisões

Decisões de arquitetura da SolidaryTech. Cada uma registra **o que foi decidido, o que foi descartado e por quê**, desde a primeira linha de código, e não retroativamente.

A série continua o raciocínio do [registro do ToggleMaster](https://github.com/fiap-tech-challenge-devops/.github/tree/main/docs/decisoes), onde estão as 39 decisões das Fases 3 e 4. Quando a SolidaryTech resolve algo de forma diferente, a ADR daqui cita a de lá e diz o motivo da diferença.

## Formato

Um arquivo por decisão, em `NNNN-titulo-em-kebab.md`. O número é a **ordem de registro**: nunca é renumerado, nunca é reaproveitado.

Na linha 3, sempre: `**Status:** … · **Domínio:** … · **Data:** … · **Fase:** 5`, com `**Validação:**` quando a decisão faz uma afirmação que a execução pode confirmar ou desmentir.

Seções: **Contexto · Decisão · Alternativas descartadas · Consequências · O que esta decisão não resolve · Onde aprofundar.**

- **Alternativas descartadas é obrigatória.** Decisão sem alternativa descartada é relato, não decisão
- **O que esta decisão não resolve** é o que impede a ADR de parecer mais confiante do que é
- **Uma página.** Se não cabe, provavelmente são duas decisões

### Status e Validação são eixos diferentes

`Status` responde **"isto me vincula?"**: `aceita`, `revertida`, `superada por NNNN`, ou `aceita, exceto em X, superado pela NNNN`.

`Validação` responde **"a execução já confirmou?"**: `em execução`, `não validada`, `parcial`. Ausente significa que não há o que validar, não que ninguém testou.

Uma decisão pode estar aceita e não validada. Misturar os dois eixos num campo só faz o índice mentir.

### Decisão aceita não é reescrita, é superada

A antiga é marcada e ganha, no topo, uma nota datada dizendo **o que caiu e o que continua valendo**. A segunda metade é a que evita jogar fora a parte certa de uma decisão errada. O texto original não é editado.

### Correção factual não é decisão superada

Número errado, citação torta, nome de recurso que nunca existiu: corrige-se no lugar, sem nota. O teste: se a frase corrigida muda **o que foi decidido ou por quê**, é superação; se muda só o que é verdade sobre o mundo, é correção.

### O que também se registra

- **A decisão de não fazer.** É o registro que mais se perde e o que mais evita retrabalho
- **A previsão que falhou.** Quando a execução desmente o que uma ADR previu, isso vira ADR nova citando a anterior, com o mecanismo, não só o resultado

---

## Por domínio

As tabelas são escritas à mão: a descrição de cada linha diz por que aquela decisão importa **naquele domínio**, e isso não sai do título.

### CI/CD

| # | Decisão | Status |
|---|---|---|
| [0001](0001-servicos-juntos-plataforma-separada.md) | Serviços juntos exigem CI por subdiretório e filtro de caminho por serviço | aceita |
| [0002](0002-base-do-togglemaster-copiada-para-a-org.md) | Os workflows do ToggleMaster são copiados e adaptados aqui, por tag nova | aceita |

### GitOps

| # | Decisão | Status |
|---|---|---|
| [0001](0001-servicos-juntos-plataforma-separada.md) | O estado desejado do cluster tem repositório próprio, e o CD escreve nele | aceita |

### Disaster Recovery

| # | Decisão | Status |
|---|---|---|
| [0003](0003-dr-em-warm-standby-em-outra-regiao.md) | Warm standby em outra região, e o dado das doações replicado fora do cluster | aceita |

### IaC

| # | Decisão | Status |
|---|---|---|
| [0002](0002-base-do-togglemaster-copiada-para-a-org.md) | Os módulos do ToggleMaster são copiados para a org, consumidos sempre por tag | aceita |
| [0003](0003-dr-em-warm-standby-em-outra-regiao.md) | A região é parâmetro desde o primeiro módulo; estado da região secundária independente | aceita |

---

## Por fase

### Fase 5 — Hackathon

| # | Decisão | Data |
|---|---|---|
| [0001](0001-servicos-juntos-plataforma-separada.md) | Os três serviços num repositório; plataforma em repositórios próprios | 24/11 |
| [0002](0002-base-do-togglemaster-copiada-para-a-org.md) | Módulos e workflows do ToggleMaster copiados para a org | 24/11 |
| [0003](0003-dr-em-warm-standby-em-outra-regiao.md) | Disaster Recovery por warm standby em outra região | 24/11 |

---

## Como acrescentar

1. Use o próximo número livre. Nunca reaproveite um número
2. Preencha a linha 3 do cabeçalho, com `Validação` se houver o que validar
3. Preencha as seis seções, **alternativas descartadas** incluída
4. Acrescente uma linha em cada tabela de domínio declarado, com a descrição do ponto de vista daquele domínio, e na tabela da fase
5. Se superar uma decisão existente, marque a antiga e escreva a nota no topo dela. Não apague
