# Comandos Docker

Um pouco sobre o Docker 

Site com containers já prontos: [Repositórios](https://hub.docker.com)

> No Docker para o Windows, geralmente é usado a virtualização por HyperV, o que pode comprometer o uso de outros programas que utilizam da virtualização do sistema operacional. Para que não tenha problemas com essas virtualizações, o Docker possui um pacote para virtualbox, o Docker Toolbox. 

## Comandos básicos para começar com o Docker

Mostra os Containers ativos na sua máquina

```
docker ps 
```

Mostra todos os Containers na sua máquina

```
docker ps --all
```

Ativa o Container

```
docker start NomeContainer
```

Criar um Container com banco de dados Postgres

```
docker run --name NomeContainer -e POSTGRES_PASSWORD=docker -p 5432:5432 -d postgres:11
```

