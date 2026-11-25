# 0007 — Imagens base fixadas por tag e digest, pela segurança da cadeia de suprimentos

**Status:** aceita · **Domínio:** Segurança · Serviços · **Data:** 25/11/2026 · **Fase:** 5

## Contexto

Toda imagem dos serviços parte de uma imagem base de terceiros: `golang` e `python` no Docker Hub, `distroless` no registry do Google. A [0005](0005-imagens-minimas-com-cache.md) deixou em aberto como fixá-las.

Uma tag de registry é um ponteiro mutável. Quem controla o repositório de origem, ou quem o comprometer, pode reapontar a mesma tag para outro conteúdo, e o próximo build passa a usar uma base diferente sem nenhuma mudança no Dockerfile. No distroless isso é a regra, não a exceção: as tags não têm versão, e `static-debian13:nonroot` muda sempre que a imagem é reconstruída.

O ToggleMaster fixava pela tag completa, sem digest (`golang:1.25.14-alpine`, `alpine:3.19`). O `alpine:3.19` saiu de suporte em 11/2025 e continuou no Dockerfile, porque uma tag congelada não avisa quando envelhece.

O projeto já protege a outra ponta: a imagem publicada tem o SHA do commit como tag, em ECR imutável, para que a imagem auditada seja a que roda ([0020 do ToggleMaster](https://github.com/fiap-tech-challenge-devops/.github/blob/main/docs/decisoes/0020-tag-da-imagem-e-o-sha.md)). A base não tinha a mesma garantia.

## Decisão

Toda imagem base é referenciada por **tag e digest**: `imagem:tag@sha256:…`.

O motivo é **segurança**:

- **Imutabilidade.** O digest identifica o conteúdo. Uma tag reapontada no registry de origem não muda o que o build usa
- **O que foi varrido é o que foi construído.** O resultado do Trivy vale para aquela base exata, e não para o que a tag apontar amanhã
- **Rastreabilidade.** A tag diz para o leitor qual versão é; o digest garante para a máquina que é aquela

A atualização dos digests é **manual**.

## Alternativas descartadas

**Só a tag completa, como no ToggleMaster.** Legível, mas não protege contra o reapontamento, e não fixa nada no distroless.

**Só o digest.** Imutável, mas ilegível: ninguém sabe que versão está rodando sem consultar o registry, e uma revisão de código não consegue avaliar a mudança.

**Dependabot atualizando os digests toda semana, num pull request agrupado.** Resolveria o envelhecimento silencioso da base, ao custo de cerca de um pull request por semana para revisar, um deploy semanal só de imagem base e um ajuste no CI de pull request, que hoje faz login na AWS e não receberia os secrets numa execução disparada pelo Dependabot. Descartado porque esse custo operacional é desnecessário para um projeto de pós-graduação.

## Consequências

- O digest congela a base inteira, inclusive as vulnerabilidades corrigidas depois. A imagem só recebe correção do sistema base quando alguém atualiza o digest
- O gate do Trivy em CRITICAL é a rede de proteção: uma vulnerabilidade crítica descoberta na base reprova a esteira, e é esse o gatilho prático para atualizar
- Atualizar uma base passa a ser uma mudança explícita e revisável no Dockerfile

## O que esta decisão não resolve

- Vulnerabilidades HIGH ou menores na base não reprovam a esteira, e sem atualização periódica podem se acumular

## Onde aprofundar

[0005](0005-imagens-minimas-com-cache.md), as imagens que usam estas bases.
