# Custos Estimados – Toggle Master (AWS)

> Região de referência: **us-east-2 (Ohio)**

> Modelo de cobrança: **On-Demand**

> Data de referência: **maio/2026**

---

## Resumo Executivo

| Cenário | Custo Mensal Estimado |
|---|---|
| Dentro do Free Tier (primeiros 12 meses) | **~$2,30/mês** |
| Fora do Free Tier | **~$21,41/mês** |

---

## Detalhamento por Recurso

### EC2 – Instância de Aplicação

| Item | Detalhe | Custo/hora | Custo mensal (730h) |
|---|---|---|---|
| Instância `t3.micro` | 2 vCPU, 1 GB RAM | $0,0104 | ~$7,59 |
| Armazenamento EBS (8 GB gp3) | Volume raiz padrão | $0,08/GB-mês | ~$0,64 |
| **Subtotal EC2** | | | **~$8,23/mês** |

> **Free Tier:** 750 horas/mês de `t3.micro` + 30 GB EBS gratuitos nos primeiros 12 meses → **$0,00/mês**

[Ver preços EC2 On-Demand (us-east-2)](https://aws.amazon.com/ec2/pricing/on-demand/)

---

### RDS – Banco de Dados PostgreSQL

| Item | Detalhe | Custo/hora | Custo mensal (730h) |
|---|---|---|---|
| Instância `db.t3.micro` | Single-AZ, PostgreSQL | $0,016 | ~$11,68 |
| Armazenamento gp2 (20 GB) | Volume de banco de dados | $0,115/GB-mês | ~$2,30 |
| Backups automatizados | Até 20 GB | Gratuito | $0,00 |
| **Subtotal RDS** | | | **~$13,98/mês** |

> **Free Tier:** 750 horas/mês de `db.t3.micro` + 20 GB de armazenamento gratuitos nos primeiros 12 meses → **~$2,30/mês** (apenas o storage acima do gratuito pode ser cobrado se ultrapassado)

[Ver preços RDS PostgreSQL (us-east-2)](https://aws.amazon.com/rds/postgresql/pricing/)

---

### Rede e Infraestrutura

| Recurso | Custo |
|---|---|
| VPC | Gratuito |
| Subnets (pública + privadas) | Gratuito |
| Internet Gateway | Gratuito |
| Route Tables | Gratuito |
| Security Groups | Gratuito |
| NAT Gateway | **Não utilizado** (economia intencional) |

[Ver preços VPC e Rede](https://aws.amazon.com/vpc/pricing/)

---

### Transferência de Dados

| Item | Free Tier | Após Free Tier |
|---|---|---|
| Dados de entrada (inbound) | Gratuito | Gratuito |
| Dados de saída – primeiros 100 GB/mês | Gratuito | — |
| Dados de saída – acima de 100 GB/mês | — | $0,09/GB |
| Tráfego interno (EC2 ↔ RDS na mesma VPC) | Gratuito | Gratuito |

> Para um ambiente de aprendizado/challenge com baixo volume de tráfego, o custo de transferência de dados tende a ser **$0,00/mês**.

[Ver preços de transferência de dados](https://aws.amazon.com/ec2/pricing/on-demand/#Data_Transfer)

---

## Comparativo: Free Tier vs. Após Free Tier

| Recurso | Free Tier (12 meses) | Após Free Tier |
|---|---|---|
| EC2 `t3.micro` (730h) | $0,00 | $7,59 |
| EBS 8 GB | $0,00 | $0,64 |
| RDS `db.t3.micro` (730h) | $0,00 | $11,68 |
| RDS Storage 20 GB | ~$2,30 | $2,30 |
| Transferência de dados | $0,00 | $0,00 |
| **Total estimado** | **~$2,30/mês** | **~$22,21/mês** |

---
