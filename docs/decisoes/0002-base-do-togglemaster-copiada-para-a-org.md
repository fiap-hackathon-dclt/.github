# 0002 — Módulos e workflows do ToggleMaster copiados para a org, não referenciados de lá

**Status:** aceita · **Domínio:** IaC · CI/CD · **Data:** 24/11/2026 · **Fase:** 5

## Contexto

A biblioteca de módulos Terraform e os workflows de CI/CD do ToggleMaster são públicos e versionados por tag: [`terraform-aws-modules`](https://github.com/fiap-tech-challenge-devops/terraform-aws-modules) em `v0.1.0`, [`reusable-workflows`](https://github.com/fiap-tech-challenge-devops/reusable-workflows) em `v2.4.0`. Os dois cobrem quase tudo o que o enunciado da Fase 5 pede: VPC, EKS, RDS, SQS, DynamoDB, ECR e IRSA de um lado; CI com DevSecOps, CD por GitOps e esteira de Terraform do outro.

O ToggleMaster continua ativo depois do curso, como portfólio do grupo, e vai evoluir por conta própria.

A SolidaryTech já sabe que vai precisar mudar as duas bases. Os workflows de CI precisam rodar num subdiretório ([0001](0001-servicos-juntos-plataforma-separada.md)), o `cd` tem o `togglemaster-gitops` como repositório padrão, e as tags obrigatórias de FinOps precisam alcançar todos os recursos.

## Decisão

Copiar as duas bases para a org `fiap-hackathon-dclt`, nas tags `v0.1.0` e `v2.4.0`, **sem alteração**. Toda adaptação para a SolidaryTech entra como tag nova nos repositórios daqui.

Os consumidores (`solidarytech-iac` e `solidarytech-microservices`) referenciam as cópias daqui, sempre por tag. A regra do ToggleMaster de nunca referenciar por `main` ([0014](https://github.com/fiap-tech-challenge-devops/.github/blob/main/docs/decisoes/0014-referencias-fixadas-por-tag.md)) continua valendo.

## Alternativas descartadas

**Referenciar os repositórios do ToggleMaster direto, por tag, entre orgs.** Era o correto do ponto de vista de biblioteca: uma cópia só, sem duplicação. Foi descartado porque toda adaptação da SolidaryTech teria de ser feita nos repositórios do ToggleMaster, acoplando dois projetos com ciclos de vida diferentes. Uma correção para o hackathon viraria tag nova numa biblioteca que outro projeto consome. Além disso, a esteira da SolidaryTech dependeria das configurações de Actions de outra organização.

**Fork pelo GitHub.** Preservaria o histórico de commits e a ligação visível com a origem. Não foi a escolha porque o que se quer do ToggleMaster é o ponto de partida, não sincronização contínua: as duas bases vão divergir. A origem fica registrada pela tag, no README de cada repositório.

## Consequências

- Código duplicado entre as duas orgs. Uma correção feita num lado não chega ao outro sozinha
- O histórico de commits começa do zero aqui. O raciocínio anterior continua acessível pelas ADRs do ToggleMaster, citadas por link
- A documentação interna (`docs/` e `example/` dos workflows, `README.md` e `example/` dos módulos) ainda descreve o ToggleMaster, e é atualizada junto com cada peça adaptada
- Enquanto a primeira tag não existir nesta org, nenhum consumidor consegue referenciar estas cópias

## O que esta decisão não resolve

- O esquema de versionamento das tags daqui: se continua a numeração da origem ou recomeça
- Quais módulos da origem não serão usados, por exemplo `elasticache` e `ec2`, e se saem da cópia

## Onde aprofundar

[`terraform-aws-modules/README.md`](https://github.com/fiap-hackathon-dclt/terraform-aws-modules) e [`reusable-workflows/README.md`](https://github.com/fiap-hackathon-dclt/reusable-workflows).
