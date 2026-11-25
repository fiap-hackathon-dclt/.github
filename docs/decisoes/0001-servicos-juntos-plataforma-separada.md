# 0001 — Os três serviços num repositório; infraestrutura, GitOps e documentação em repositórios próprios

**Status:** aceita, exceto no conteúdo do `.github`, superado pela 0004 · **Domínio:** CI/CD · GitOps · **Data:** 24/11/2026 · **Fase:** 5

> **25/11/2026 — superada em parte pela [0004](0004-documentos-de-entrega-no-relatorio.md).** Caiu: o `.github` guardar documentos transversais; os documentos de entrega vão no relatório da fase, fora do Git. Continua valendo: toda a organização dos repositórios, e o `.github` como perfil da organização e registro de decisões.

## Contexto

O curso entregou os três microsserviços num único repositório, [`dougls/hackathon-DCLT`](https://github.com/dougls/hackathon-DCLT). O ToggleMaster, de onde vem a base técnica deste projeto, usava um repositório por serviço, mais repositórios próprios para infraestrutura, GitOps, módulos, workflows e documentação.

O enunciado não prescreve a organização. Ele fala em "o repositório" ao listar os entregáveis e pede no relatório um link para o código-fonte, mas não exige que tudo more num lugar só.

Três forças pesaram:

**O CD escreve no repositório GitOps.** No ToggleMaster ([0020](https://github.com/fiap-tech-challenge-devops/.github/blob/main/docs/decisoes/0020-tag-da-imagem-e-o-sha.md)), o CD grava o SHA da imagem num repositório diferente do código. O estado desejado do cluster tem histórico próprio, e o commit de promoção não se mistura com o de desenvolvimento.

**Infraestrutura tem outro ritmo.** O Terraform sobe e desce o ambiente diariamente; o código dos serviços muda por outro motivo e em outra cadência.

**Os workflows de CI do ToggleMaster rodam na raiz do repositório.** O `go-ci` e o `python-ci` não recebem diretório como parâmetro. Verificado na tag `v2.4.0`.

## Decisão

| Repositório | Conteúdo |
|---|---|
| `solidarytech-microservices` | Os três serviços, cada um no seu diretório, com esteira própria disparada por filtro de caminho |
| `solidarytech-iac` | Terraform |
| `solidarytech-gitops` | Charts e Applications do Argo CD |
| `terraform-aws-modules` | Biblioteca de módulos |
| `reusable-workflows` | Workflows de CI/CD compartilhados |
| `.github` | Perfil da organização, registro de decisões e documentos transversais |

O [perfil da organização](https://github.com/fiap-hackathon-dclt) é a entrada única: é o link que vai para o relatório, e ele mapeia todos os repositórios.

## Alternativas descartadas

**Um repositório por serviço, como no ToggleMaster.** Reaproveitaria o `go-ci` e o `python-ci` sem nenhuma alteração, e foi a recomendação inicial por isso. A equipe preferiu manter a base do curso como uma unidade: com três serviços, separá-los multiplica repositórios, esteiras e configurações sem ganho de isolamento que o hackathon precise. **A ressalva fica registrada:** a escolha custa uma adaptação nos workflows compartilhados, que no ToggleMaster não existiu.

**Tudo num repositório só: serviços, Terraform, GitOps e documentação.** Daria um único link para o relatório, que é o que o singular do enunciado sugere. Foi descartado por três motivos. Mistura quatro ciclos de vida num histórico só. O commit do CD voltaria para o repositório que dispara o CI, exigindo filtros para não entrar em loop. E a role da AWS assumida por OIDC passaria a valer para qualquer workflow do repositório, porque o trust da role se restringe por repositório e por branch, nunca por pasta.

## Consequências

- O `go-ci` e o `python-ci` precisam aceitar o diretório do serviço. Não basta um `working-directory`: o contexto do build Docker, o alvo do Trivy e o diretório do linter também mudam. Entra como tag nova do `reusable-workflows`
- Cada serviço tem um workflow chamador com filtro `paths:`, para que uma mudança no `ngo-service` não reconstrua o `donation-service`
- O CD continua escrevendo em outro repositório, então continua precisando de um token com escrita no `solidarytech-gitops`, como no ToggleMaster
- Os três serviços compartilham o mesmo sujeito OIDC. A role de CI que publica imagem não distingue qual serviço está publicando

## O que esta decisão não resolve

- A divisão do Terraform em stages
- A separação de permissões por serviço dentro do mesmo repositório, se ela se mostrar necessária
- Proteção de branch e checks obrigatórios em cada repositório

## Onde aprofundar

[`reusable-workflows/README.md`](https://github.com/fiap-hackathon-dclt/reusable-workflows#o-que-precisa-mudar), seção *O que precisa mudar*, e a [0002](0002-base-do-togglemaster-copiada-para-a-org.md), que trata de onde vêm os módulos e os workflows.
