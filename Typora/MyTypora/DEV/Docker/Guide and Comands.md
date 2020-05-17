# Guide e Comandos Docker

## Comandos Principais

```js
//Verifivar Containers ativos
docker ps    ||   docker ps -a

//Iniciando um container
docker start <id do container>

//Parando a exec de um container
docker stop <id do container>
    
//Delete Container
docker rm <id do container>
    
//Procurando uma imagem
docker search nginx

//Baixar uma Imagem
docker pull nginx

//Verificando imagens que existem no PC
docker images

//exec cmd in container
docker run -it <id do container>   ||  exit or ctrl+D //sair

//Identificar IP VM docker
docker-machine ip  

//Identificar a porta do container
docker port <id do container>
```

---

## Executando uma imagem

Para rodar uma imagem, utilizamos o comando run, mas ele pode exigir alguns parâmetros adicionais, dependendo do que você quer fazer. No caso, como vamos executar um servidor web, precisaremos do comando abaixo:

```js
docker run --name my_nginx -d -p 8080:80 nginx

--name my_nginx//nome do container
-d //Não atrela o terminal ao processo
-p 8080:80 //porta que seu pc vai usar para conectar com a outra
nginx //nome ou caminho do container
-P //a porta fica aleatória
```

Neste comando, os parâmetros utilizados foram:

- **–name**: o nome que você dará para esta instancia do nginx.

- **–d**: indica que a imagem será executada em background (daemon)

- **–p**: faz um redirecionamento de portas. Nele indicamos que todas as requisições que chegarem na porta 8080, da sua maquina local, deverão ser redirecionadas para a porta 80 do container que estamos criando.

- **nginx**: imagem que será utilizada para criação deste container. Se ela não existir na sua maquina, o Docker vai baixa-la automaticamente.

  
---
## Procurando Imagem no Docker HUB

[Docker Hub](https://hub.docker.com/)



---

## Instalar Imagens já prontas

```js
//Criando um Container Docker
docker run --name database -e POSTGRES_PASSWORD=docker -p 5432:5432 -d postgres:11


```



---

## Código de Conexão para algumas Linguagens

### Server Node

```js
module.exports = {
    dialect: 'postgres',
    host: '192.168.99.100',
    username: 'postgres',
    password: 'docker',
    database: 'gobarber2',
    define: {
        timestamps: true,
        underscored: true,
        underscoredAll: true,
    }
}
```



----

## Variaveis do container na maquina Local

```js
docker run -p 8080:3000 -v "$(pwd):/var/www" -w "/var/www" node npm start
```

