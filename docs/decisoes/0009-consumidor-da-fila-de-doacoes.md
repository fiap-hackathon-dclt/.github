# 0009 — Um consumidor da fila de doações, para o tracing atravessar microsserviços

**Status:** aceita · **Domínio:** Observabilidade · Serviços · **Validação:** não validada · **Data:** 27/11/2026 · **Fase:** 5

## Contexto

O enunciado exige demonstrar distributed tracing e pede que a observabilidade permita *"rastrear requisições entre microsserviços"* (critério 5).

Os três serviços foram desenhados independentes: o README do curso fala em *"3 microsserviços independentes"*, e nenhum chama outro. Instrumentados como estão, cada trace teria um serviço só.

O próprio curso deixou uma ponta solta: descreve o `donation-service` como responsável pela *"publicação de eventos assíncronos em filas para processamento posterior"*. O produtor existe; o consumidor, não. Hoje cada doação aprovada vira uma mensagem na fila com a doação em JSON (`id`, `ngo_id`, `amount`, `donor_name`, `status`, `created_at`), e nada lê essa mensagem.

No ToggleMaster, o mesmo problema foi resolvido assim: o `evaluation-service` publicava no SQS, o `analytics-service` consumia, e uma avaliação virava um trace só, atravessando a fila.

## Decisão

Criar um consumidor da fila de doações que, para cada evento:

1. busca a ONG do `ngo_id` no `ngo-service`;
2. se a ONG existe, registra a notificação de que ela recebeu uma doação. O envio real fica simulado;
3. se não existe, registra a doação como suspeita.

O `donation-service` passa a enviar o contexto do trace nos atributos da mensagem, e o trace atravessa três serviços: `donation-service`, consumidor e `ngo-service`.

O caminho crítico da doação não muda: o `donation-service` continua sem depender de nenhum outro serviço para gravar e responder.

A implementação entra na frente de observabilidade.

## Alternativas descartadas

**Não mexer, e demonstrar o tracing dentro de cada serviço** (HTTP, banco, fila). Não exige código novo, mas deixa fraco o "entre microsserviços" do critério 5.

**O `donation-service` chamar o `ngo-service` antes de gravar.** Dá um trace síncrono e corrige a falta de validação da ONG, mas põe uma dependência no caminho crítico: com o `ngo-service` fora, as doações param. Isso contraria a continuidade operacional que o enunciado destaca.

**Consumidor que totaliza a arrecadação por ONG.** Daria um número de negócio para o pitch, mas exige um armazenamento novo, e não exercita a chamada ao `ngo-service`.

**Consumidor que grava uma trilha de auditoria no S3.** Parece ajudar o DR, mas não serve como backup confiável: o produtor pode perder evento antes de ele chegar à fila.

## Consequências

- Um componente a mais para construir, testar, implantar e monitorar
- A conferência da ONG passa a acontecer depois da gravação, sem bloquear a doação. Uma doação para ONG inexistente continua sendo aceita, e é marcada em seguida
- A fila ganha um destino. Isso abre caminho para escalar o consumidor pelo tamanho da fila (KEDA, já usado no ToggleMaster), para ter uma *dead-letter queue* (suportada pelo módulo `sqs`) e para usar a idade da mensagem mais antiga como indicador de saúde
- A fila Standard pode entregar a mesma mensagem mais de uma vez: o consumidor precisa tolerar duplicatas, usando o `id` da doação

## O que esta decisão não resolve

- O evento perdido na origem: se o `donation-service` falhar ao publicar, o evento nunca chega à fila
- Linguagem, localização no repositório e nome do consumidor
- O que "registrar como suspeita" significa na prática, e onde isso fica guardado
- O contrato do evento: tipo, versão e atributos da mensagem
- Escalonamento por KEDA, *dead-letter queue* e indicador de idade da fila: cada um, se for feito, é decisão própria

## Onde aprofundar

O fluxo da doação no board de estudo do Miro, e o [desafio 0004](../desafios/0004-erro-do-banco-some-do-log.md) para o comportamento atual do `donation-service`.
