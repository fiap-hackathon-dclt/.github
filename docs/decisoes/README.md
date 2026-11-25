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

### Item em aberto que foi respondido

Quando uma pergunta de *O que esta decisão não resolve* é respondida, o texto original fica, porque era verdadeiro quando foi escrito, e a resposta entra **logo abaixo, em citação e com data**. Não é correção nem superação: a decisão continua intacta, só o mundo mudou de estado. A data importa porque o valor do registro é saber quando se passou a saber.

### O que também se registra

- **A decisão de não fazer.** É o registro que mais se perde e o que mais evita retrabalho
- **A previsão que falhou.** Quando a execução desmente o que uma ADR previu, isso vira ADR nova citando a anterior, com o mecanismo, não só o resultado

---

## Por domínio

As tabelas são escritas à mão: a descrição de cada linha diz por que aquela decisão importa **naquele domínio**, e isso não sai do título.

### CI/CD

| # | Decisão | Status |
|---|---|---|
| [0001](0001-servicos-juntos-plataforma-separada.md) | Serviços juntos exigem CI por subdiretório e filtro de caminho por serviço | aceita, exceto no `.github` |
| [0002](0002-base-do-togglemaster-copiada-para-a-org.md) | Os workflows do ToggleMaster são copiados e adaptados aqui, por tag nova | aceita |
| [0005](0005-imagens-minimas-com-cache.md) | O build da imagem no CI ganha cache de camadas por serviço | aceita |

### GitOps

| # | Decisão | Status |
|---|---|---|
| [0001](0001-servicos-juntos-plataforma-separada.md) | O estado desejado do cluster tem repositório próprio, e o CD escreve nele | aceita, exceto no `.github` |

### Documentação

| # | Decisão | Status |
|---|---|---|
| [0004](0004-documentos-de-entrega-no-relatorio.md) | SLI/SLO/SLA, forecast, PCN e ciclo de incidentes vivem no relatório; no Git, só código, READMEs e ADRs | aceita |

### Disaster Recovery

| # | Decisão | Status |
|---|---|---|
| [0003](0003-dr-em-warm-standby-em-outra-regiao.md) | Warm standby em outra região, e o dado das doações replicado fora do cluster | aceita |

### IaC

| # | Decisão | Status |
|---|---|---|
| [0002](0002-base-do-togglemaster-copiada-para-a-org.md) | Os módulos do ToggleMaster são copiados para a org, consumidos sempre por tag | aceita |
| [0003](0003-dr-em-warm-standby-em-outra-regiao.md) | A região é parâmetro desde o primeiro módulo; estado da região secundária independente | aceita |

### Observabilidade

| # | Decisão | Status |
|---|---|---|
| [0006](0006-go-1-27-python-3-13.md) | A linha atual do OTel, sem a trava de versão do ToggleMaster | aceita |

### Segurança

| # | Decisão | Status |
|---|---|---|
| [0007](0007-imagem-base-por-tag-e-digest.md) | O digest torna a base imutável: tag reapontada na origem não chega ao build | aceita |

### Serviços

| # | Decisão | Status |
|---|---|---|
| [0005](0005-imagens-minimas-com-cache.md) | Go em distroless `static`, Python em Alpine, multi-stage e sem root | aceita |
| [0006](0006-go-1-27-python-3-13.md) | Go 1.27 e Python 3.13, com as dependências atualizadas junto | aceita |
| [0007](0007-imagem-base-por-tag-e-digest.md) | Toda imagem base no Dockerfile como `imagem:tag@sha256:…` | aceita |
| [0008](0008-ambiente-local-com-emuladores.md) | O sistema roda local em docker-compose, com DynamoDB Local e ElasticMQ, sem conta na AWS | aceita |

---

## Por fase

### Fase 5 — Hackathon

| # | Decisão | Data |
|---|---|---|
| [0001](0001-servicos-juntos-plataforma-separada.md) | Os três serviços num repositório; plataforma em repositórios próprios | 24/11 |
| [0002](0002-base-do-togglemaster-copiada-para-a-org.md) | Módulos e workflows do ToggleMaster copiados para a org | 24/11 |
| [0003](0003-dr-em-warm-standby-em-outra-regiao.md) | Disaster Recovery por warm standby em outra região | 24/11 |
| [0004](0004-documentos-de-entrega-no-relatorio.md) | Documentos de entrega no relatório da fase, não no Git | 25/11 |
| [0005](0005-imagens-minimas-com-cache.md) | Imagens multi-stage mínimas, sem root, com cache | 25/11 |
| [0006](0006-go-1-27-python-3-13.md) | Go 1.27 e Python 3.13, dependências atualizadas junto | 25/11 |
| [0007](0007-imagem-base-por-tag-e-digest.md) | Imagens base fixadas por tag e digest | 25/11 |
| [0008](0008-ambiente-local-com-emuladores.md) | Ambiente local em docker-compose, com emuladores da AWS | 25/11 |

---

## Como acrescentar

1. Use o próximo número livre. Nunca reaproveite um número
2. Preencha a linha 3 do cabeçalho, com `Validação` se houver o que validar
3. Preencha as seis seções, **alternativas descartadas** incluída
4. Acrescente uma linha em cada tabela de domínio declarado, com a descrição do ponto de vista daquele domínio, e na tabela da fase
5. Se superar uma decisão existente, marque a antiga e escreva a nota no topo dela. Não apague
