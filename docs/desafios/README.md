# Desafios

Problemas reais encontrados na construção da SolidaryTech, e como foram resolvidos. Centralizados aqui para o projeto inteiro: serviços, infraestrutura, GitOps e esteiras.

Um desafio conta **o que aconteceu**. Quando a solução envolve uma escolha entre alternativas, a escolha vira [ADR](../decisoes/), e o desafio aponta para ela.

## Formato

Um arquivo por desafio, em `NNNN-titulo-em-kebab.md`, numerado pela ordem de registro. O número nunca é reaproveitado.

Seções: **Sintoma · Investigação · Causa · Solução · Como verificar.** O sintoma vem com a evidência: a mensagem de erro, o log ou a saída do comando, como apareceu.

## Índice

| # | Desafio | Onde |
|---|---|---|
| [0001](0001-donation-service-nao-compila.md) | O `donation-service` entregue pelo curso não compila | `donation-service` |
| [0002](0002-psycopg2-sem-wheel-no-alpine.md) | O `psycopg2-binary` 2.9.5 não tem wheel para Python 3.13 no Alpine | `ngo-service` |
| [0003](0003-gunicorn-27-control-socket.md) | O gunicorn 27 tenta criar um socket de controle no home do usuário | `ngo-service`, `volunteer-service` |
| [0004](0004-erro-do-banco-some-do-log.md) | O erro de conexão com o banco some do log | `donation-service` |
