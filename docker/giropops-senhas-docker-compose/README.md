# Giropops Senhas App

## Visão Geral

Este projeto sobe uma aplicação chamada **giropops-senhas** integrada com um **Redis**, utilizando Docker Compose.
A aplicação é construída a partir de um Dockerfile customizado e se comunica com o Redis via rede interna do Docker.

---

## Arquitetura

A stack é composta por:

- **giropops-senhas**
  - Aplicação principal
  - Expõe porta `5000`
  - Consome Redis

- **redis**
  - Banco em memória
  - Persistência habilitada (AOF)

- **network (giropops)**
  - Rede bridge customizada
  - Subnet definida manualmente

- **volume (strigus)**
  - Persistência de dados via bind mount no host

---

## Pré-requisitos

- Docker instalado
- Docker Compose (plugin `docker compose`)

---

## Estrutura do Projeto

- ├── docker-compose.yml
- │ └── Dockerfile

---

## Como Executar

### 1. Subir os containers

- ```docker compose up -d```

### 2. Verificar containers

- ```docker ps```

### 3. Acessar aplicação

Abra no navegador:

- ```http://localhost:5000```

### Variáveis de Ambiente

As variáveis podem ser definidas no arquivo .env.

Exemplo:

- ```REDIS_HOST=redis```

### Persistência de Dados

O projeto utiliza um volume chamado strigus, que na prática é um bind mount apontando para:

- ```/home/$USER/docker```

Isso significa:

- Os dados ficam persistidos no host
- Necessário garantir permissões corretas

### Rede

A aplicação utiliza uma rede customizada:

- Nome: ```giropops```
- Subnet: ```172.20.0.0/16```

Isso permite controle maior sobre IPs e comunicação interna.

### Dependências

A aplicação depende do Redis:

- O ```depends_on``` garante ordem de start
- O healthcheck do Redis valida disponibilidade

### Recursos e Limites

Configurações definidas:

CPU:
- ```Reserva: 0.25```
- ```Limite: 0.5```

Memória:
- ```Reserva: 128MB```
- ```Limite: 256MB```

Observação: essas configs só têm efeito em Docker Swarm


### Restart Policy

Reinicia apenas em falha
- Até 3 tentativas
- Delay de 5 segundos

### Observações Importantes

- Deploy não funciona com docker compose up, apenas em Swarm
- Volume está acoplado ao host (baixa portabilidade)
- ```depends_on``` não garante que o Redis esteja pronto (apenas iniciado)
- DNS custom configurado (Google)

### Parar o Ambiente

- ```docker compose down```

### Troubleshooting
Ver logs

- ```docker compose logs -f```

### Verificar Redis

- ```docker exec -it <container_redis> redis-cli ping```

Resposta esperada:

- ```PONG ```
