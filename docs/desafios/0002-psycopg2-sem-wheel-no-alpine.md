# 0002 — O `psycopg2-binary` 2.9.5 não tem wheel para Python 3.13 no Alpine

**Serviço:** `ngo-service` · **Data:** 25/11/2026 · **Fase:** 5

## Sintoma

Nenhum build chegou a quebrar: o problema foi encontrado antes, ao planejar a imagem Alpine com Python 3.13 da [ADR 0005](../decisoes/0005-imagens-minimas-com-cache.md) e da [ADR 0006](../decisoes/0006-go-1-27-python-3-13.md).

## Investigação

O `ngo-service` fixava `psycopg2-binary==2.9.5`. O `-binary` só é binário quando o PyPI tem uma *wheel* pronta para a combinação de Python e sistema. O Alpine usa `musl` em vez de `glibc`, e precisa de wheels marcadas `musllinux`.

A lista de arquivos do pacote no PyPI mostra a primeira wheel `cp313-musllinux` na versão `2.9.10`. A `2.9.5` não tem nenhuma.

## Causa

Sem wheel compatível, o `pip` cai para o código-fonte e tenta compilar a extensão em C. Isso exige `gcc`, `musl-dev` e `libpq-dev` no estágio de build, e a `libpq` na imagem final.

## Solução

`psycopg2-binary==2.9.13`, a versão atual, que tem wheel `musllinux` para Python 3.13, 3.14 e 3.15. A imagem continua sem compilador.

## Como verificar

```bash
docker build -t ngo-service ngo-service
```

O passo do `pip install` termina sem compilar nada, e o serviço conecta no PostgreSQL ao subir.
