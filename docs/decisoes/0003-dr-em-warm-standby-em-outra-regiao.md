# 0003 — Disaster Recovery por warm standby em outra região, não por Velero

**Status:** aceita · **Domínio:** Continuidade · IaC · **Validação:** não validada · **Data:** 24/11/2026 · **Fase:** 5

## Contexto

O enunciado exige que o processamento de doações continue diante da indisponibilidade do cluster principal, com RTO e RPO definidos **para os dados das doações** no Plano de Continuidade de Negócios. Para a parte prática, ele oferece duas opções, e a equipe precisa implementar e demonstrar uma:

- **Opção A:** Velero fazendo backup do estado do cluster, dos manifestos e dos volumes persistentes para um bucket externo
- **Opção B:** Terraform modularizado para provisionar um ambiente espelho em outra região, em warm standby, inicializado com um único comando

As doações ficam no PostgreSQL gerenciado do `donation-service`, **fora do cluster**. Os serviços não guardam estado em volume do Kubernetes.

## Decisão

Opção B. A infraestrutura é escrita com a **região como parâmetro** desde o primeiro módulo, para que a mesma composição suba na região secundária. O dado das doações é protegido por replicação ou cópia de snapshot do banco para a região secundária.

A implementação fica para depois da fundação. O que vale desde já é a restrição sobre o Terraform: nada pode assumir `us-east-1` fixo.

## Alternativas descartadas

**Opção A, Velero.** É mais barata e mais rápida de demonstrar. Foi descartada porque não protege o que o PCN pede: o Velero cobre o que está **dentro** do cluster, e as doações estão no RDS. O RPO das doações ficaria sem resposta, a menos que se acrescentasse a cópia do banco, e nesse ponto a peça que importa já não seria o Velero. Restaurar o estado do cluster também é o que menos importa num sistema GitOps: o Argo CD reconstrói os manifestos a partir do repositório.

**Ativo-ativo nas duas regiões.** Daria RTO perto de zero, mas exige escrita em duas regiões ou failover automático do banco, e dobra o custo contínuo. O enunciado pede warm standby, e o crédito da conta não comporta dois ambientes ligados.

## Consequências

- Todo stage e todo módulo recebem a região por variável; nomes de recursos não podem colidir entre regiões
- O estado do Terraform da região secundária precisa ser independente do da primária, para que a perda de uma não impeça o `apply` da outra
- A réplica do banco ou a cópia de snapshot gera custo contínuo, e entra no forecast de FinOps
- O enunciado aceita demonstrar os módulos prontos para a região secundária, sem mantê-la ligada. A demonstração com o ambiente de pé é desejável, mas não obrigatória

## O que esta decisão não resolve

- Qual é a região secundária
- Réplica de leitura entre regiões ou cópia periódica de snapshot. A escolha define o RPO e o custo contínuo
- O que acontece com a fila SQS e o DynamoDB na troca de região
- Como o tráfego muda de região: DNS, health check e quem dispara a troca
- Os valores de RTO e RPO, que dependem das respostas acima

## Onde aprofundar

O Plano de Continuidade de Negócios, quando for escrito em `docs/continuidade/`.
