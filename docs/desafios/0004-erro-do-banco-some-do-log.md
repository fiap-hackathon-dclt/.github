# 0004 — O erro de conexão com o banco some do log

**Serviço:** `donation-service` · **Data:** 25/11/2026 · **Fase:** 5

## Sintoma

Com o banco fora do ar, o `donation-service` parava na subida, mas o log não dizia por quê:

```
Erro ao conectar ao banco de dados: <nil>
```

Reproduzido com a imagem da `main` e uma `DATABASE_URL` apontando para um host que não existe.

## Investigação

O código de subida era:

```go
db, err := sql.Open("pgx", dbURL)
if err != nil || db.Ping() != nil {
    log.Fatalf("Erro ao conectar ao banco de dados: %v", err)
}
```

O `sql.Open` do Go **não conecta**: só valida a URL e prepara o pool. Ele devolve `err == nil` mesmo com o banco inalcançável. Quem abre a primeira conexão é o `Ping`.

Quando o `Ping` falhava, a condição do `if` era verdadeira, mas o erro dele era descartado ali mesmo. O `log.Fatalf` imprimia o `err` do `sql.Open`, que era `nil`.

## Causa

Dois erros diferentes testados na mesma condição, e só o primeiro guardado numa variável.

## Solução

Cada erro verificado e impresso separadamente, no [PR #1](https://github.com/fiap-hackathon-dclt/solidarytech-microservices/pull/1):

```go
db, err := sql.Open("pgx", dbURL)
if err != nil {
    log.Fatalf("Erro ao abrir conexão com o banco de dados: %v", err)
}
if err := db.Ping(); err != nil {
    log.Fatalf("Erro ao conectar ao banco de dados: %v", err)
}
```

| Cenário | Antes | Depois |
|---|---|---|
| Banco inalcançável | `<nil>` | `lookup banco-inexistente ... no such host` |
| Senha errada | não testado | `password authentication failed for user "solidary"` |

## Como verificar

```bash
docker build -t donation-service donation-service
docker run --rm -e DATABASE_URL="postgres://x:y@banco-inexistente:5432/db" donation-service
```

O log mostra o motivo real da falha, e não `<nil>`.
