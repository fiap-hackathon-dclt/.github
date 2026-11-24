# .github

Repositório central da **SolidaryTech**, o Hackathon da Fase 5 da POSTECH FIAP (DevOps/SRE).

Ele tem dois papéis:

- **Perfil da organização.** O [`profile/README.md`](profile/README.md) é a página de entrada da org no GitHub, com a equipe, a aplicação e o mapa dos repositórios.
- **Documentação transversal.** O que atravessa mais de um repositório mora aqui, e não em nenhum deles sozinho.

## O que vai morar aqui

| Caminho | Conteúdo |
|---|---|
| `docs/decisoes/` | Registro de decisões de arquitetura (ADRs), um arquivo por decisão, com índice por domínio e por fase |
| `docs/sre/` | Definição formal de SLI, SLO e SLA do `donation-service`, cálculo do error budget e a estratégia de redução de MTTR |
| `docs/finops/` | Política de tags, forecast de custo mensal, análise de rightsizing e recomendações de otimização |
| `docs/continuidade/` | Plano de Continuidade de Negócios, com RTO e RPO dos dados de doações, e a estratégia de Disaster Recovery |
| `docs/itsm/` | Ciclo de vida do incidente, da detecção por AIOps ao post-mortem |

Os diretórios são criados à medida que cada frente produz conteúdo real. Um diretório vazio aqui seria uma promessa, e a documentação deste projeto só descreve o que está implementado.

## Registro de decisões

Cada decisão registra **o que foi decidido, o que foi descartado e por quê**. Uma decisão aceita nunca é reescrita: ela é superada por outra, e a antiga fica como histórico, com uma nota dizendo o que caiu e o que continua valendo.

As regras de formato seguem as do [registro do ToggleMaster](https://github.com/fiap-tech-challenge-devops/.github/tree/main/docs/decisoes), que é a origem técnica deste projeto.
