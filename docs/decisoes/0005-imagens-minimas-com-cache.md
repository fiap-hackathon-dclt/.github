# 0005 — Imagens multi-stage mínimas, sem root, com cache de dependências no Dockerfile e no CI

**Status:** aceita · **Domínio:** Serviços · CI/CD · **Validação:** não validada · **Data:** 25/11/2026 · **Fase:** 5

## Contexto

O enunciado exige Dockerfiles otimizados para os três serviços, e o README do curso pede multi-stage e redução de vulnerabilidades.

No ToggleMaster, as imagens eram multi-stage com Alpine na final, usuário próprio e `wget` instalado. Dois pontos não se repetem aqui:

- O Dockerfile Go rodava `go mod tidy` depois de copiar o código. Qualquer mudança no código invalidava a camada de dependências, e o build podia alterar o `go.mod`
- O `go-ci` e o `python-ci` constroem a imagem com `docker/build-push-action` sem `cache-from` nem `cache-to`: todo build no CI começa do zero

## Decisão

| | `donation-service` (Go) | `ngo-service`, `volunteer-service` (Python) |
|---|---|---|
| **Imagem final** | `gcr.io/distroless/static-debian13`, variante `nonroot` | Alpine, como no ToggleMaster |
| **Build** | Binário estático, `CGO_ENABLED=0`, `-trimpath -ldflags="-s -w"` | Dependências instaladas no estágio de build; só o resultado vai para a final, sem `pip` |
| **Usuário** | UID 65532 da variante `nonroot` | Usuário próprio com UID fixo |

Em todos:

- **Manifesto de dependências antes do código.** `go.mod`/`go.sum` ou `requirements.txt` são copiados e instalados primeiro; o código vem depois. Mudança só de código reaproveita a camada de dependências
- **Cache montado no build** para módulos e compilação do Go. Ajuda o build local
- **Cache de camadas do GitHub Actions** (`type=gha`) no `build-push-action`, com escopo por serviço. É o que persiste entre execuções no runner
- **`USER` declarado** na imagem final. Nenhum serviço roda como root

## Alternativas descartadas

**Alpine também no Go.** Traz shell e gerenciador de pacotes que o binário estático não usa, e cada pacote é superfície para o Trivy acusar. A falta de shell no distroless não pesa: a sonda de saúde do Kubernetes é por HTTP.

**Distroless no Python.** Prende a versão do Python à imagem Debian do distroless. O Alpine já foi validado com OTel no ToggleMaster.

**Slim no Python.** Mais compatível, mas maior e com mais pacotes de sistema, contra o objetivo de imagem mínima.

**Só o cache montado, sem cache no CI.** O cache montado não sobrevive entre execuções no runner do GitHub sem ferramenta extra.

## Consequências

- Sem shell na imagem Go, não há `kubectl exec` para depurar. A investigação passa pelos traces, logs e métricas
- O `go-ci` e o `python-ci` ganham `cache-from` e `cache-to`, junto com o parâmetro de subdiretório da [0001](0001-servicos-juntos-plataforma-separada.md)
- O cache do GitHub Actions tem limite de tamanho por repositório; os três serviços dividem esse espaço

## O que esta decisão não resolve

- Se as imagens base serão fixadas por tag de versão ou por digest
- O tamanho final e o tempo de build com e sem cache, que só a execução vai medir

## Onde aprofundar

[0006](0006-go-1-27-python-3-13.md), as versões de linguagem que essas imagens usam.
