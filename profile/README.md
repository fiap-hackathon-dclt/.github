<div align="center">

# SolidaryTech

**A plataforma que conecta ONGs, doadores e voluntários, e a engenharia de confiabilidade, custo e continuidade que a mantém de pé quando o país inteiro resolve doar ao mesmo tempo.**

![AWS](https://img.shields.io/badge/AWS-EKS·RDS·DynamoDB·SQS-FF9900?logo=amazonwebservices&logoColor=white)
![Terraform](https://img.shields.io/badge/Terraform-modular-7B42BC?logo=terraform&logoColor=white)
![Kubernetes](https://img.shields.io/badge/Kubernetes-EKS-326CE5?logo=kubernetes&logoColor=white)
![Argo CD](https://img.shields.io/badge/Argo%20CD-GitOps-EF7B4D?logo=argo&logoColor=white)
![GitHub Actions](https://img.shields.io/badge/GitHub%20Actions-DevSecOps-2088FF?logo=githubactions&logoColor=white)
![Go](https://img.shields.io/badge/Go-donation--service-00ADD8?logo=go&logoColor=white)
![Python](https://img.shields.io/badge/Python-Flask-3776AB?logo=python&logoColor=white)
![OpenTelemetry](https://img.shields.io/badge/OpenTelemetry-traces·métricas·logs-425CC7?logo=opentelemetry&logoColor=white)
![SRE](https://img.shields.io/badge/SRE-SLI·SLO·SLA-2E7D32)
![FinOps](https://img.shields.io/badge/FinOps-tags·forecast·rightsizing-00897B)

</div>

---

## Quem somos

| Participante | LinkedIn |
|---|---|
| **Vitor Fontes do Prado** | [in/vitor-prado-889046172](https://www.linkedin.com/in/vitor-prado-889046172) |
| **Clayton Lima** | [in/clayton-lima-b1a8b7178](https://www.linkedin.com/in/clayton-lima-b1a8b7178/) |
| **Felipe Lima da Silva** | [in/felipelima285](https://www.linkedin.com/in/felipelima285/) |
| **Lucas Rodrigues** | [in/lucas-rodrigues-6278a855](https://www.linkedin.com/in/lucas-rodrigues-6278a855/) |
| **Thiago Saraiva** | [in/saraivathiago](https://www.linkedin.com/in/saraivathiago/) |

---

## Sobre o projeto

A SolidaryTech é o **Hackathon da Fase 5 da pós-graduação em DevOps/SRE (POSTECH FIAP)**: o projeto final, que integra tudo o que foi construído nas quatro fases anteriores.

O cenário é uma iniciativa sem fins lucrativos que conecta ONGs, doadores e voluntários em todo o Brasil. Depois de aparecer em rede nacional, a plataforma passou a receber picos imprevisíveis de acesso, e a diretoria pediu quatro coisas:

| Necessidade | O que significa na prática |
|---|---|
| **Continuidade operacional** | Se a nuvem cair, o processamento de doações não pode parar: Disaster Recovery, com RTO e RPO definidos para os dados das doações |
| **Controle financeiro** | Todo recurso identificado por tag, custo projetado e workloads dimensionados pelo consumo medido, não por palpite |
| **Gestão preditiva de incidentes** | Detectar anomalia antes que o usuário perceba, com AIOps, e um ciclo de incidente que vai do alerta ao post-mortem |
| **Acordos de serviço** | SLI, SLO e SLA claros com as ONGs parceiras, medidos de verdade e com error budget calculado |

A regra que organiza tudo continua a mesma do primeiro dia do grupo: **se não está no código, não existe.** Nada é criado no console da nuvem nem aplicado com `kubectl` na mão.

---

## Linhagem

Este projeto não começa do zero. As Fases 1 a 4 foram entregues no **[ToggleMaster](https://github.com/fiap-tech-challenge-devops)**, e de lá vêm a biblioteca de módulos Terraform, os workflows de CI/CD, o modelo de GitOps, a stack de observabilidade e 39 registros de decisão sobre o que funcionou, o que foi descartado e o que a execução desmentiu.

Quando a SolidaryTech resolve algo de forma diferente do ToggleMaster, o registro de decisões diz o que mudou e por quê.

---

## A aplicação

| Serviço | Stack | Estado | Responsabilidade |
|---|---|---|---|
| `ngo-service` | Python · Flask | PostgreSQL | Cadastro e gestão das ONGs parceiras |
| `donation-service` | Go | PostgreSQL · SQS | Processamento das doações e publicação de eventos. **É o caminho crítico** |
| `volunteer-service` | Python · Flask | DynamoDB | Cadastro de voluntários por ONG |

O `donation-service` é o componente que recebe prioridade em toda decisão de arquitetura: é onde estão os SLOs, o error budget e o RPO do plano de continuidade.

O código de partida é o fornecido pelo curso em [`dougls/hackathon-DCLT`](https://github.com/dougls/hackathon-DCLT).

---

## Os repositórios

**Plataforma**

| Repositório | Papel |
|---|---|
| [`terraform-aws-modules`](https://github.com/fiap-hackathon-dclt/terraform-aws-modules) | Biblioteca de módulos Terraform reutilizáveis, consumidos por tag |
| [`solidarytech-iac`](https://github.com/fiap-hackathon-dclt/solidarytech-iac) | Provisiona a infraestrutura AWS, com as tags FinOps obrigatórias e a região de DR |
| [`solidarytech-gitops`](https://github.com/fiap-hackathon-dclt/solidarytech-gitops) | Manifests e charts que o Argo CD sincroniza com o cluster |
| [`reusable-workflows`](https://github.com/fiap-hackathon-dclt/reusable-workflows) | Workflows de CI/CD compartilhados pelos serviços e pela IaC |

**Aplicação**

| Repositório | Papel |
|---|---|
| [`solidarytech-microservices`](https://github.com/fiap-hackathon-dclt/solidarytech-microservices) | Os três microsserviços, cada um no seu diretório e com esteira própria |

**Documentação**

| Repositório | Papel |
|---|---|
| [`.github`](https://github.com/fiap-hackathon-dclt/.github) | Este perfil, o registro de decisões e os documentos transversais: SRE, FinOps, PCN e ITSM |

---

## O desafio, frente a frente

O enunciado organiza a avaliação em cinco frentes. A tabela é atualizada conforme cada uma ganha evidência real, não quando é planejada.

| Frente | Escopo | Estado |
|---|---|---|
| **Fundação DevOps** | Docker, Kubernetes, Terraform, CI/CD com DevSecOps, GitOps, observabilidade e APM com distributed tracing | em andamento: repositórios criados |
| **SRE** | SLIs e SLOs do `donation-service`, SLA, dashboard SRE, error budget e redução de MTTR | não iniciada |
| **FinOps** | Tags obrigatórias, rightsizing por métrica medida, forecast mensal e recomendação nativa do provedor | não iniciada |
| **ITSM e AIOps** | Detecção de anomalia por IA do APM e ciclo de vida do incidente até o post-mortem | não iniciada |
| **Multicloud, Segurança e DR** | Plano de Continuidade de Negócios, RTO e RPO das doações, backup e recuperação demonstráveis | não iniciada |

---

## Documentação

O [registro de decisões](https://github.com/fiap-hackathon-dclt/.github/tree/main/docs/decisoes) guarda, desde a primeira linha de código, o que foi escolhido, o que foi descartado e por quê, incluindo as decisões de **não** fazer e as previsões que a execução desmentiu.
