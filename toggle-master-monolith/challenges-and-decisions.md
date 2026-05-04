# Desafios e Decisões de Arquitetura

## Desafios Encontrados

### 1. Conectividade EC2 para RDS

**Problema:** O banco de dados não estava acessível a partir da instância EC2.

**Solução:** Ajuste do Security Group do RDS para aceitar conexões exclusivamente originadas da EC2, restringindo o acesso por IP de origem.

---

### 2. Acesso SSH

**Problema:** Falha na conexão SSH à instância EC2.

**Solução:** Revisão e correção das regras do Security Group associado à EC2, com uso correto da chave privada `.pem` correspondente ao key pair configurado na instância.

---

### 3. Configuração de Subnets

**Problema:** Dificuldade em separar corretamente as camadas pública e privada da rede.

**Solução:**
- EC2 alocada na subnet pública para receber tráfego externo
- RDS alocado em subnets privadas, sem exposição à internet

---

### 4. Banco de Dados Inexistente

**Problema:** Erro `database does not exist` ao tentar conectar a aplicação ao RDS.

**Solução:** Criação manual do banco de dados após o provisionamento da instância RDS:

```sql
CREATE DATABASE togglemaster;
```

---

### 5. Ambiente Python e Sistema Operacional

Inicialmente, a instância EC2 foi provisionada com Ubuntu 24 por ser elegível ao Free Tier.

**Problema:**
- Python 3.14 incompatível com `psycopg2`
- Falhas na instalação das dependências da aplicação

**Decisão:** Evitar soluções de gerenciamento de versão como `pyenv` para manter a configuração simples.

**Solução:** Migração da instância para Amazon Linux, seguida de configuração manual do ambiente Python e instalação bem-sucedida das dependências.

---

### 6. Porta 5000 Ocupada

**Problema:** Erro `Connection in use` ao iniciar a aplicação na porta 5000 em mais de um terminal.

**Solução:** Identificação e encerramento do processo que ocupava a porta.

---

### 7. Chave SSH Incorreta

**Problema:** Tentativa de conexão SSH com uma chave que não correspondia à instância.

**Solução:** Verificação do key pair correto no console da AWS e uso da chave `.pem` associada à instância em questão.

---

## Decisões de Arquitetura

### Separação de Camadas

A arquitetura segue o princípio de separação de responsabilidades:

- **EC2** — camada de aplicação, responsável por executar a API
- **RDS** — camada de dados, responsável pelo armazenamento persistente

### Segurança

- Banco de dados isolado em subnets privadas, sem acesso direto à internet
- Acesso ao RDS restrito via Security Groups, permitindo apenas tráfego originado da EC2

### Uso de Múltiplas Subnets Privadas

A configuração com mais de uma subnet privada prepara a infraestrutura para alta disponibilidade com Multi-AZ, seguindo boas práticas da AWS mesmo em ambientes de desenvolvimento.

### Escolha de Instâncias

- `t3.micro` para EC2 e RDS
- Seleção motivada pelo custo reduzido e elegibilidade ao Free Tier

### Exposição da Aplicação

- Porta 5000 aberta para fins de teste e validação
- Em ambiente de produção, recomenda-se o uso de um Load Balancer (ALB) com HTTPS

---

## Conclusão

A arquitetura implementada atende aos objetivos do projeto, garantindo:

- Isolamento de rede entre camadas
- Segurança no acesso ao banco de dados
- Comunicação interna eficiente entre os componentes
- Simplicidade operacional e baixo custo

Os desafios enfrentados ao longo da implementação contribuíram para aprofundar o entendimento sobre redes na AWS, configuração de Security Groups, gerenciamento de ambiente Python e integração entre aplicação e banco de dados relacional.
