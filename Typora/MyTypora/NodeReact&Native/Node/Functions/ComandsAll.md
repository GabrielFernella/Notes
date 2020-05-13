# Todos os Comandos Utilizados

## Dependências

Usar

```js
npm i
npx 
yarn 
```

```js
//Server
npm init -y //Init package.json
express  // API Rest
sequelize //ORM Node for database
pg pg-hstore // Drive Postgres 
bcryptjs // criptografia
yup //Scchema Validation
multer //Lida com arquivos
jsonwebtoken //criptografia Token JWT

//Dev
sequelize-cli -D //CLI terminal sequelize
sucrase -D // recursos mais aruais do javascript
nodemon -D // reinicia o servidor express
```

## Windows

```js
//Lista o processo da porta
	netstat -a -n -o

//Mata o Processo da porta
	taskkill /PID processo /F

```



## Sequelize

```js
//exibe todas as funções do sequelize, create e etc
yarn sequelize -h

//Gera uma database apartir dos dados passados em configuração
yarn sequelize db:create

//Criando uma Migration pelo CLI
yarn sequelize migration:create --name=create-users

//Rodar a migration para o banco de dados
yarn sequelize db:migrate

//Para desfazer a ultima migration
yarn sequelize db:migrate:undo || yarn sequelize db:migrate:undo:all

//Criar a migration de endereços do usuário
yarn sequelize migration:create --name=create-addresses
```

## Adonis

```js
//Mostra todos os Comandos Adonis
adonis new -h

//Cria Projeto Simples
adonis new myProject --api-only

//Instala pg do postgres para trabalhar no Adonis
npm i --save pg

//Exec server adonis, se tirar o --dev ele roda em produção
adonis serve --dev

//Cria uma Migration para ser editada
adonis migration:run

//Cria um Controller pelo terminal com o nome de User
adonis make:controller User

//Mostrar as Rotas disponíveis da sua aplicação
adonis route:list
```

## Knex

## 







