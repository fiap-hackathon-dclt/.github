# 0008 — Ambiente local em docker-compose, com DynamoDB Local e ElasticMQ no lugar da AWS

**Status:** aceita · **Domínio:** Serviços · **Data:** 25/11/2026 · **Fase:** 5

## Contexto

O sistema precisa rodar inteiro na máquina de cada integrante antes de ir para o cluster: os três serviços, o PostgreSQL, o DynamoDB do `volunteer-service` e a fila SQS do `donation-service`.

O README do curso mandava criar a tabela DynamoDB e a fila SQS na AWS de verdade. Isso exige credenciais e gasta crédito, e o enunciado proíbe criar recurso pelo console.

O emulador mais conhecido, o LocalStack, teve o repositório open source arquivado. O uso gratuito passou a ser um plano da empresa, e cada um dos cinco integrantes precisaria aderir a ele.

## Decisão

Um `docker-compose.yml` na raiz do `solidarytech-microservices`, que constrói os três serviços pelos próprios Dockerfiles e sobe:

| Peça | Imagem | Papel |
|---|---|---|
| PostgreSQL | `postgres:16.16-alpine3.24` | Uma instância com os dois bancos, `ngo_db` e `donation_db`, criados pelos `init.sql` dos serviços |
| DynamoDB | `amazon/dynamodb-local:3.3.2` | Emulador mantido pela AWS, em memória |
| SQS | `softwaremill/elasticmq-native:1.7.1` | Emulador compatível com SQS, com a fila declarada em arquivo |
| Criação da tabela | `amazon/aws-cli:2.42.3` | Contêiner que roda uma vez, cria a tabela se ela não existir, e termina |

Os serviços apontam para os emuladores pelas variáveis `AWS_ENDPOINT_URL_SQS` e `AWS_ENDPOINT_URL_DYNAMODB`, que os SDKs leem sozinhos. O código não sabe que está rodando localmente.

As imagens do compose são fixadas **só por tag de versão**, sem digest. Elas não vão para produção, e a [0007](0007-imagem-base-por-tag-e-digest.md) trata das imagens base dos serviços.

## Alternativas descartadas

**LocalStack.** Um contêiner só para os dois serviços da AWS, mas o open source foi arquivado e o uso gratuito passa por um plano da empresa.

**DynamoDB e SQS de verdade na AWS.** Fidelidade total, mas custa crédito, exige credenciais em todas as máquinas e recursos de desenvolvimento criados pelo Terraform.

**Uma instância de PostgreSQL por serviço.** Isolamento que não traz nada localmente, ao custo de mais memória.

**Digest também nas imagens do compose.** Consistência com a 0007, mas sem necessidade para ferramentas que só rodam na máquina de quem desenvolve.

## Consequências

- `docker compose up --build` sobe o sistema inteiro, sem conta na AWS e sem configuração manual
- O ambiente local é efêmero: o DynamoDB roda em memória e o PostgreSQL recria os bancos quando o volume é novo
- A versão do PostgreSQL local precisa acompanhar a do RDS, que ainda não foi decidida
- O ElasticMQ e o DynamoDB Local emulam a API, não o comportamento completo da AWS. Limites, latência e permissões IAM só aparecem no cluster

## O que esta decisão não resolve

- A versão do PostgreSQL de produção
- Se o compose também vai rodar a stack de observabilidade, quando os serviços forem instrumentados

## Onde aprofundar

O `docker-compose.yml` e a pasta `local/` do [`solidarytech-microservices`](https://github.com/fiap-hackathon-dclt/solidarytech-microservices).
