# Montando uma API CRUD utilizando o Knex.js

## ❓ O que, pra que e por que?

- Query Builder
- Construtor de queries SQL com Javascript
- Callback style or Promise style
- Multiplas plataformas (PostgreSQL, MySQL, SQLite3...)
- Agilidade
- Ajuda em todos os cenários? Não , mas ainda assim você pode escrever raw queries
- SQL Raw pode ser perigoso se não for feito com cuidado, pode ser feio, e de difícil manutenção
- Knex vs SQL Raw

```sql
knex('users').where('id', 1)

select * from `users` where `id` = 1
```

## Começando

Para dar inicio aos nossos estudos,vamos montar uma pequena estrutura para começar o projeto.

Crie sua pasta onde estará armazenando sua API da seguinte forma: Myapi/src/server.js

Após criar o arquivo, vá ao terminal e digite:

>Para instalar o package menager

```
npm init -y 
```

Altere os Campos main e scripts do package menager

```json
"main": "src/server.js",
"scripts": {
    "start": "nodemon src/server.js"
```

Instale o Nodemon como dependência de desenvolvimento

```
npm install nodemon -D
```

Agora temos que instalar mais alguns pacotes npm, são eles:

```
npm install knex pg express
```

>knex - Query Builder
>
>pg - Driver para banco de dados postgres
>
>express - framework

O próximo comando irá executar o knex para criar um arquivo de configuração para o knex

```
npx knex init
```

vc deixará o arquivo knexfile.js da seguinte maneira

```js
// Update with your config settings.

module.exports = {

  development: {
    client: 'pg',
    connection: {
      host : '192.168.99.100',
      port : '5432',
      user : 'postgres',
      password : 'docker',
      database : 'knexDatabase'
    },
    migrations: {
      tableName: 'migrations',
      directory: `${__dirname}/src/database/migrations`
    },
    seeds: {
      directory: `${__dirname}/src/database/seeds`
    },
  }
};
```

---

## Criando a Conexão com o Database

O próximo passo é criar o arquivo onde ficara as configurações de conexão no seguinte caminho: src/database/index.js, insira o seguinte código no arquivo:

```js
const knexfile = require('../../knexfile');
const knex = require('knex')(knexfile.development);

module.exports = knex;
```

---

## Criação das Migrations

Rodaremos um comando no terminal para criar a migration a partir do knex na pasta migration dentro de database.

```
npx knex migrate:make create_table_users
```

Edite o documento da seguinte maneira

```js
//utilizando arrow function
exports.up = knex => knex.schema.createTable('users', function(table){
        table.increments('id')
        table.text('username').unique().notNullable()

        table.timestamp('created_at').defaultTo(knex.fn.now())
        table.timestamp('updated_at').defaultTo(knex.fn.now())
    })
    

exports.down = knex => knex.schema.dropTable('users')
```

Execute o seguinte comando para validar a estrutura e passar a migration para o banco de dados

```
npx knex migrate:latest
```

Pronto, agora vc já tem sua tabela criada no banco de dados, se ocorreu tudo com sucesso vc pode verificar a tabela na sua database.

---

## Utilizando o Seeds

Rode o seguinte comando para criar os Seeds

```
npx knex seed:make 001_users
```

Este comando monstará uma estrutura de inserção de dados em tabela, o código a baixo já está editado para o propósito da nossa API

```js
exports.seed = function(knex) {
  // Deletes ALL existing entries
  return knex('users').del()
    .then(function () {
      // Inserts seed entries
      return knex('users').insert([
        { username: 'Gabs'},
        { username: 'Mila'},
        { username: 'Mayk'},
      ]);
    });
};

```

Para rodar esse seed basta digitar no terminal

```
npx knex seed:run
```

---

## Criando Controllers e estruturando melhor a aplicação

Teremos que estruturar nossa aplicação utilizando boas práticas.

Criaremos uma pasta adicional chamada **Controller** e dentro terá um arquivo *UserController.js* e colocaremos o seguinte código.

### Controller

Caminho: src/controllers/UserController.js

```js
const knex = require('../database')

module.exports = {
    async index(req,res) {
        const results = await knex('users')

        return res.json(results)
    }
}
```

### Rotas

Caminho:  src/routes.js

```js
const express = require('express')
const routes = express.Router()

const UserController = require('./controllers/UserController')

routes.get('/users', UserController.index)


module.exports = routes
```

### Server

Caminho:  src/server.js

```js
const express = require('express')
const routes = require('./routes')

const app = express()

app.use(express.json())
app.use(routes)


app.listen(3333, ()=> console.log('server is running'));
```

---

## Captura de Erros

Utilizando Middleware para captura de erros

No arquivo de server, depois de usar "app.use(routes)" acrescente esse comando

```js
//catch all
app.use((error,req,res,next) => {
    res.status(error.status || 500)
    res.json({ error: error.message})
})
```

Este Middleware utilizaremos para tratar os erros que podem retornar dos nossos controllers, com o uso o TryCatch

---

## Fazendo os Métodos CRUD

Agora vamos Fazer o Método CRUD para a tabela de users

### Controller

```js
const knex = require('../database')

module.exports = {
    async index(req,res) {
        const results = await knex('users')

        return res.json(results)
    },

    async create(req,res,next){
        try {
            const { username } = req.body

            await knex('users').insert({
                username
            })

            return res.status(201).send()
        } catch (error) {
            next(error)
        }
    },
    async update(req,res,next){
        try {
            const { username } = req.body
            const { id } = req.params

            await knex('users')
                .update({ username })
                .where({ id })

            return res.send()
            
        } catch (error) {
            next(error)
        }
    },
    async delete(req,res){
        try {
            const { id } = req.params
            await knex('users')
                .where({id : id}) //outro exemplo
                .del()

            return res.send()
        } catch (error) {
            next(error)
        }
    },
}
```

### Routes

```js
const express = require('express')
const routes = express.Router()

const UserController = require('./controllers/UserController')

routes.get('/users', UserController.index)
routes.post('/users', UserController.create)
routes.put('/users/:id', UserController.update)
routes.delete('/users/:id', UserController.delete)


module.exports = routes
```

### Server

```js
const express = require('express')
const routes = require('./routes')

const app = express()

app.use(express.json())
app.use(routes)

//catch all
app.use((error,req,res,next) => {
    res.status(error.status || 500)
    res.json({ error: error.message})
})


app.listen(3333, ()=> console.log('server is running'));
```





