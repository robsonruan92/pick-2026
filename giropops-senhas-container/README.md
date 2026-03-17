# README.md  
  
## Visão geral  
  
Este guia descreve o processo de build e execução de uma aplicação Flask containerizada utilizando Docker. A aplicação depende de um serviço Redis externo para funcionamento.
  
---  
  
# Estrutura do Dockerfile
    
```bash  
FROM python:3.11-slim

WORKDIR /app
COPY requirements.txt .
COPY app.py .
COPY templates templates/
COPY static static/

RUN pip install --no-cache-dir -r requirements.txt

EXPOSE 5000

CMD ["flask", "run", "--host=0.0.0.0"]
```

Descrição:
- **FROM python:3.11-slim**: imagem base com Python
- **WORKDIR /app**: define diretório de trabalho no container
- **COPY**: copia arquivos da aplicação para dentro do container
- **RUN pip install**: instala dependências do projeto
- **EXPOSE 5000**: documenta a porta utilizada pela aplicação
- **CMD**: inicia a aplicação Flask

----------

# Passos para execução  
  
## 1. Build da imagem Docker
  
```
docker image build -t giropops-senhas:1.0 .
```
Cria uma imagem Docker com a tag giropops-senhas:1.0 a partir do Dockerfile.

----------

## 2. Identificar IP da máquina
```bash  
ip a
```
Utilizado para identificar o IP local da máquina, necessário para configurar a variável de ambiente do Redis.

----------

## 3. Subir container da aplicação
```
docker run -d \
  --name giropops-senhas \
  -p 5000:5000 \
  --env REDIS_HOST=192.168.15.8 \
  giropops-senhas:1.0
```
Executa o container da aplicação:

- **-d**: modo detached
- **--nam**e: nome do container
- **-p 5000:5000**: mapeamento de porta
- **--env REDIS_HOST**: define o host do Redis

----------

## 4. Subir container do Redis
```
docker run -d --name redis -p 6379:6379 redis
```
Executa o Redis em container:

- Porta padrão 6379 exposta
- Container separado da aplicação

----------

## 5. Validar Redis
```
curl localhost:6379
```
Teste simples de conectividade com o Redis.

Obs: Redis não responde HTTP, então o retorno esperado não é um conteúdo válido, apenas validação de conexão.

----------

## 6. Instalar Redis
```
sudo apt-get install redis
```
Instala o **Redis**, utilizado pela aplicação como armazenamento em memória ou cache.

----------

## 7. Acessar aplicação
```
http://127.0.0.1:5000/
```
A aplicação estará disponível via navegador ou curl.

----------
