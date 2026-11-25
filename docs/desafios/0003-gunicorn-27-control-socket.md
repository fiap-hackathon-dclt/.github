# 0003 — O gunicorn 27 tenta criar um socket de controle no home do usuário

**Serviços:** `ngo-service`, `volunteer-service` · **Data:** 25/11/2026 · **Fase:** 5

## Sintoma

Com as imagens novas, os serviços Python subiam e respondiam, mas o log registrava um erro a cada inicialização:

```
[ERROR] Control server error: [Errno 13] Permission denied: '/home/app'
```

## Investigação

O gunicorn 27 introduziu uma interface local de controle por socket Unix. O `gunicorn --help` mostra o caminho padrão:

```
--control-socket PATH   [/home/app/.gunicorn/gunicorn.ctl]
--no-control-socket     Disable control socket. [False]
```

O usuário da imagem foi criado sem diretório home (`adduser -H`), para a imagem final não ter nada gravável além do necessário. O gunicorn não consegue criar `/home/app/.gunicorn`.

## Causa

Um recurso novo do gunicorn com um padrão que assume usuário com home, contra uma imagem que roda sem root e sem home.

## Solução

`--no-control-socket` no `CMD` dos dois Dockerfiles. No Kubernetes, quem controla o processo é o próprio cluster, e a interface não seria usada.

A alternativa era criar o home do usuário. Ela foi descartada porque mantém uma interface de administração sem uso dentro do contêiner.

## Como verificar

```bash
docker run --rm -e DATABASE_URL=... ngo-service
```

O log de inicialização não tem nenhuma linha `ERROR`.
