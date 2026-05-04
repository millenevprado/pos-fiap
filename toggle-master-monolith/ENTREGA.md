# Relatório de Entrega – Toggle Master (AWS)

## Participantes

| Nome | Papel |
|---|---|
| Millene Viana Prado | Desenvolvedora e responsável pela infraestrutura |

---

## Links

| Recurso | Link |
|---|---|
| Vídeo de demonstração | [Assistir vídeo](https://res.cloudinary.com/dasoq8ilk/video/upload/v1777935285/demo-challenge-toggle-master-compressed_q2zvxg.mp4) |
| Repositório do projeto | [GitHub](https://github.com/millenevprado/pos-fiap/tree/master/toggle-master-monolith) |
| Documentação da arquitetura | [documentation.md](https://github.com/millenevprado/pos-fiap/blob/master/toggle-master-monolith/documentation.md) |
| Estimativa de custos | [estimated-costs.md](https://github.com/millenevprado/pos-fiap/blob/master/toggle-master-monolith/estimated-costs.md) |
| Desafios e decisões | [challenges-and-decisions.md](https://github.com/millenevprado/pos-fiap/blob/master/toggle-master-monolith/challenges-and-decisions.md) |

---

## Resumo da Solução

A aplicação Toggle Master foi migrada para a AWS utilizando uma arquitetura em camadas dentro de uma VPC dedicada (`togglemaster-vpc`, CIDR `10.0.0.0/16`):

- **EC2 (t3.micro)** na subnet pública executa a API Python com Gunicorn na porta 5000
- **RDS PostgreSQL (db.t3.micro)** em subnets privadas armazena os dados da aplicação
- **Security Groups** controlam o acesso: a EC2 aceita SSH e tráfego na porta 5000; o RDS aceita conexões PostgreSQL apenas da EC2
- **Internet Gateway** viabiliza o acesso externo à EC2

---

## Resumo dos Desafios

| # | Desafio | Solução |
|---|---|---|
| 1 | EC2 não conseguia acessar o RDS | Ajuste do Security Group do RDS para aceitar conexões somente da EC2 |
| 2 | Falha na conexão SSH | Correção das regras do Security Group e uso da chave `.pem` correta |
| 3 | Separação de subnets pública e privada | EC2 na subnet pública; RDS em subnets privadas |
| 4 | Erro `database does not exist` | Criação manual do banco via `CREATE DATABASE togglemaster;` |
| 5 | Python 3.14 incompatível com `psycopg2` no Ubuntu 24 | Migração para Amazon Linux com ambiente Python configurado manualmente |
| 6 | Porta 5000 em uso | Identificação e encerramento do processo conflitante |
| 7 | Chave SSH incorreta | Verificação do key pair correto no console da AWS |

---

## Resumo das Decisões de Arquitetura

| Decisão | Justificativa |
|---|---|
| EC2 na subnet pública, RDS em subnets privadas | Separação de camadas com isolamento do banco de dados |
| Acesso ao RDS restrito via Security Groups | Segurança sem complexidade adicional |
| Duas subnets privadas | Preparação para Multi-AZ e alta disponibilidade futura |
| Instâncias t3.micro para EC2 e RDS | Custo reduzido com elegibilidade ao Free Tier |
| Porta 5000 exposta diretamente | Simplicidade para ambiente de testes; produção exigiria ALB com HTTPS |
| Amazon Linux no lugar do Ubuntu 24 | Compatibilidade com `psycopg2` e facilidade de configuração do ambiente Python |
