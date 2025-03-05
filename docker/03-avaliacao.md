# Docker compose com Django e banco de dados

## Informações gerais

- Assunto: Docker, conteinizar aplicativos
- Disciplina: *sistemas operacionais*
- **Tarefa**:
  1. Criar um projeto django com uma aplicação web
    - alternativa, criar uma _branch_ no repositório do projeto integrador para configurar o acesso ao repositório de dados
  2. Criar um `Dockerfile` para o projeto django
  3. Criar a imagem e testar o conteiner para testar
  4. OPCIONAL, porque dependendo de como pergunta ao assistente de IA; criar um `Dockerfile` para o repositório de banco de dados
  5. Criar um `docker-compose.yml` e configurar para 2 serviços: `webapp` e `db`
  6. Configurar o arquivo django de acesso ao repositório de dados para usar o serviço docker `db`
  7. Testar o `docker-compose.yml`
  8. Relatar minimamente o que foi feito.
- **Entrega**: copia desse aquivo markdown preenchido, no seu repositório _fork_ de https://github.com/sistemas-operacionais/2024.2


## Relatório

### Aluno

- nome: Jeremias Bezerra Lucas
- matrícula: 20191014040002

### Relato
Foi realizada a configuração de um ambiente Docker para um projeto Django, incluindo a comunicação entre o serviço web e o banco de dados. Abaixo, o passo a passo:

Criação do projeto Django: O projeto foi iniciado utilizando o comando django-admin startproject, e uma aplicação foi criada dentro do projeto para implementar a lógica básica.

Dockerfile: Um Dockerfile foi criado para configurar o ambiente do contêiner Django, incluindo a instalação das dependências do projeto e a exposição da porta 8000.

Testes do contêiner: A imagem Docker foi construída e o contêiner foi testado localmente para garantir que o servidor Django estava funcionando corretamente.

Configuração do banco de dados: Optei por utilizar uma imagem pronta do PostgreSQL (postgres:13) para o banco de dados, sem a necessidade de criar um Dockerfile específico.

docker-compose.yml: O arquivo foi configurado para orquestrar dois serviços: webapp (Django) e db (PostgreSQL). A comunicação entre os serviços foi estabelecida utilizando variáveis de ambiente.

Testes do docker-compose: O docker-compose foi executado, mas enfrentei um problema inicial onde o Django não conseguia se conectar ao banco de dados. Após ajustar as configurações de rede e variáveis de ambiente, o problema foi resolvido.
### Arquivos docker e de configuração do django

Dockerfile para Django:

FROM python:3.9-slim

WORKDIR /app

COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

COPY . .

EXPOSE 8000

CMD ["python", "manage.py", "runserver", "0.0.0.0:8000"]

docker-compose.yml:

version: '3.8'

services:
  db:
    image: postgres:13
    environment:
      POSTGRES_DB: mydatabase
      POSTGRES_USER: myuser
      POSTGRES_PASSWORD: mypassword
    volumes:
      - postgres_data:/var/lib/postgresql/data/

  webapp:
    build: .
    command: python manage.py runserver 0.0.0.0:8000
    volumes:
      - .:/app
    ports:
      - "8000:8000"
    environment:
      DB_HOST: db
      DB_NAME: mydatabase
      DB_USER: myuser
      DB_PASSWORD: mypassword
    depends_on:
      - db

volumes:
  postgres_data:

settings.py:

import os

DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.postgresql',
        'NAME': os.getenv('DB_NAME', 'mydatabase'),
        'USER': os.getenv('DB_USER', 'myuser'),
        'PASSWORD': os.getenv('DB_PASSWORD', 'mypassword'),
        'HOST': os.getenv('DB_HOST', 'db'),
        'PORT': '5432',
    }
}
**observação** coloque nomes nos arquivos antes do códigos-fonte.
