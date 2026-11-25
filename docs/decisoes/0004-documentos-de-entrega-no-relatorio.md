# 0004 — Documentos de entrega no relatório da fase, não no Git

**Status:** aceita · **Domínio:** Documentação · **Data:** 25/11/2026 · **Fase:** 5

## Contexto

O enunciado pede, no relatório em PDF, a definição formal de SLI, SLO e SLA, a análise de custos e o forecast, o Plano de Continuidade de Negócios com RTO e RPO, e o desenho do ciclo de vida de incidentes. Também exige que a documentação esteja alinhada com o que foi implementado.

A [0001](0001-servicos-juntos-plataforma-separada.md) previa que o `.github` guardaria esses documentos como "documentos transversais", seguindo o ToggleMaster, que mantinha guias em `docs/` no próprio `.github`.

O grupo, ao fim de cada fase, monta um relatório único com tudo documentado e o envia.

## Decisão

Os documentos de entrega são escritos e mantidos **no relatório da fase, fora do Git**.

No Git ficam o código, os READMEs e as ADRs. A ADR registra a decisão por trás de um documento de entrega; o relatório descreve o estado.

## Alternativas descartadas

**Documentos em Markdown no `.github`, organizados pelas frentes do enunciado.** Era o que a 0001 previa. O enunciado não exige os documentos no Git, e o grupo já consolida tudo no relatório de cada fase.

**Cada documento no repositório do que ele descreve**, como o SLO no `solidarytech-gitops` e o forecast no `solidarytech-iac`. Mesmo motivo.

## Consequências

- O relatório é a única fonte da definição de SLI, SLO, SLA, RTO e RPO. Quem lê só o GitHub não encontra esses valores escritos, e precisa do relatório
- O que é implementado como código, como os SLOs no APM e os budgets na AWS, fica no código. O relatório precisa bater com ele, e não há verificação automática disso
- Uma ADR que depende de um documento de entrega aponta para a seção do relatório, não para um link

## O que esta decisão não resolve

- Como garantir, na hora de fechar o relatório, que cada valor nele bate com o que está implementado
- Se cada repositório terá narrativas dos problemas enfrentados, como o `docs/desafios/` do ToggleMaster

  > **25/11/2026:** terá, mas centralizado num lugar só para o projeto inteiro, em [`.github/docs/desafios/`](../desafios/), em vez de um `docs/desafios/` por repositório.

## Onde aprofundar

[`.github/README.md`](../../README.md), seção *O que não mora aqui*.
