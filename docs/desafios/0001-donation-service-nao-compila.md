# 0001 — O `donation-service` entregue pelo curso não compila

**Serviço:** `donation-service` · **Data:** 25/11/2026 · **Fase:** 5

## Sintoma

O primeiro `go mod tidy` no código original, numa cópia isolada e com Go 1.27, falhou antes de baixar qualquer dependência:

```
go: errors parsing go.mod:
go.mod:19:2: require github.com/jackc/pgx/v4/stdlib: version "v4.18.3" invalid: should be v0 or v1, not v4
```

## Investigação

`github.com/jackc/pgx/v4/stdlib` não é um módulo: é um **pacote** dentro do módulo `github.com/jackc/pgx/v4`. O `go.mod` o declarava no bloco `require` como se fosse módulo próprio, e o Go recusa o arquivo inteiro.

Removida essa linha só na cópia, o `go mod tidy` passou e o build revelou um segundo erro:

```
./main.go:6:2: "fmt" imported and not used
./main.go:10:2: "strconv" imported and not used
```

Em Go, import sem uso é erro de compilação, não aviso.

## Causa

Dois defeitos independentes no código de partida: um `go.mod` editado à mão de forma inválida e dois imports órfãos. Também não havia `go.sum` no repositório.

## Solução

Na migração para Go 1.27 da [ADR 0006](../decisoes/0006-go-1-27-python-3-13.md), que já reescrevia o `go.mod`:

- `go.mod` refeito com os módulos corretos (`pgx/v5`, `aws-sdk-go-v2`) e completado pelo `go mod tidy`, que gerou o `go.sum`
- `fmt` e `strconv` removidos dos imports

## Como verificar

```bash
docker run --rm -v "$PWD/donation-service":/src -w /src golang:1.27.4-alpine3.24 \
  sh -c 'go vet ./... && CGO_ENABLED=0 go build -o /tmp/x .'
```

Sai sem erro.
