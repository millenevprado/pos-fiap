# Documentação da Arquitetura – Toggle Master (AWS)

## Visão Geral

Esta arquitetura foi projetada para executar uma aplicação backend em Python com banco de dados PostgreSQL utilizando serviços da AWS,
seguindo boas práticas de isolamento, segurança e simplicidade.

A solução é composta por:

- EC2 (camada de aplicação)
- RDS PostgreSQL (camada de dados)
- VPC com subnets públicas e privadas
- Security Groups para controle de acesso
- Internet Gateway para acesso externo

## Arquitetura de Rede

### VPC

- Nome: `togglemaster-vpc`
- CIDR: `10.0.0.0/16`
- Responsável por isolar todos os recursos

### Subnets

1. **Subnet Pública (EC2)**
   - CIDR: `10.0.1.0/24`
   - AZ: `us-east-2a`
   - Função: hospedar a instância EC2
   - Possui rota para Internet Gateway

2. **Subnets Privadas (RDS)**
   - CIDRs:
     - `10.0.2.0/24` (us-east-2a)
     - `10.0.3.0/24` (us-east-2b)
   - Função: hospedar o banco RDS
   - Sem acesso direto à internet

### Internet Gateway

- Anexado à VPC
- Permite comunicação da EC2 com a internet

### Route Tables

1. **Public Route Table**
   - `0.0.0.0/0` → Internet Gateway
   - Associada à subnet pública

2. **Private Subnets (Main Route Table - default)**
   - Apenas rota local (`10.0.0.0/16`)
   - Sem acesso externo

## Camada de Aplicação (EC2)

### Instância EC2

- Tipo: `t3.micro` (Free Tier)
- SO: Ubuntu 22.04 LTS
- Acesso: SSH (porta 22)
- Porta da aplicação: 5000

### Security Group (EC2)

- **Inbound:**
  - SSH (22) → meu IP
  - HTTP App (5000) → `0.0.0.0/0`
- **Outbound:**
  - All traffic → `0.0.0.0/0`

### Aplicação

- Linguagem: Python
- Servidor: Gunicorn
- Porta: 5000
- Executada diretamente na EC2

## Camada de Dados (RDS)

### Instância RDS

- Engine: PostgreSQL
- Tipo: `db.t3.micro` (Free Tier)
- Modo: Single-AZ
- Sem acesso público

### Security Group (RDS)

- **Inbound:**
  - PostgreSQL (5432) → Security Group da EC2
- **Outbound:**
  - All traffic → `10.0.0.0/16` (restrito à VPC)

### Conexão EC2 → RDS

A aplicação na EC2 se conecta ao RDS usando variáveis de ambiente:

```env
DB_HOST=<endpoint-do-rds>
DB_NAME=togglemaster
DB_USER=postgres
DB_PASSWORD=<senha>
```

## Segurança

- RDS em subnets privadas (sem exposição pública)
- EC2 acessível apenas via SSH restrito
- Comunicação entre serviços via Security Groups
- Credenciais não armazenadas no código

## Fluxo de Requisição

1. Usuário acessa: `http://<IP_PUBLICO_EC2>:5000`
2. EC2 recebe requisição
3. Aplicação Flask processa
4. Se necessário, consulta o RDS
5. Retorna resposta ao usuário

## Diagrama Simplificado

```
Internet
   │
   ▼
Internet Gateway
   │
   ▼
Public Subnet (EC2)
   │
   ▼
Private Subnets (RDS)
```

## Considerações

- Elastic IP pode ser usado para IP fixo (opcional)
- Aplicação roda em foreground (não persistente)
- Não há NAT Gateway (intencional para reduzir custo)
- Ambiente focado em aprendizado / challenge

## Conclusão

A arquitetura implementada segue princípios fundamentais de cloud:

- Separação de camadas
- Segurança por isolamento de rede
- Uso eficiente do Free Tier
- Simplicidade operacional

## Evoluções Possíveis

- Uso de Load Balancer
- Auto Scaling
- Deploy com Docker
- CI/CD
- Secrets Manager
- Observabilidade (CloudWatch)
