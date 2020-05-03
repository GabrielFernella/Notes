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

> Para instalar o package menager

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

> knex - Query Builder
> 
> pg - Driver para banco de dados postgres
> 
> express - framework

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

//not found - Mensagem de erro
app.use((req,res,next)=> {
    const error = new Error('Not found')
    error.status = 404
    next(error)
})

//catch all
app.use((error,req,res,next) => {
    res.status(error.status || 500)
    res.json({ error: error.message})
})


app.listen(3333, () => console.log('server is running'));
```

---

## Criando Table Projetos foreing key

Agora criaremos a tebela projetos para fazer a chave estrangeira entre projetos e users.

```powershell
npx knex migrate:make create_projects_table
```

Criará um arquivo na migration, vamos editar ele da seguinte maneira.

```js
exports.up = knex => knex.schema.createTable('projects', function(table){
    table.increments('id')
    table.text('title')

    //relacionamento 1 user pra n projetos
    table.integer('user_id')
        .references('users.id')
        .notNullable()
        .onDelete('CASCADE')

    table.timestamp(true, true)
})


exports.down = knex => knex.schema.dropTable('projects')
```

Após concluir a alteração, rode o comando ara efetivar essa tabela no banco de dados

```
npx knex migrate:latest
```

Crie um Seed para essa tabela 

```
npx knex seed:make 002_projects
```

No arquivo gerado, preencha-o da seguinte maneira.

```js
exports.seed = function(knex) {
  // Deletes ALL existing entries
  return knex('projects').del()
    .then(function () {
      // Inserts seed entries
      return knex('projects').insert([
        {
          user_id:5,
          title: "Meu projeto"
        }
      ]);
    });
};
```

> O id que usei começa do 5 pois confirme eu deletei alguns usuários, eu verifiquei no meu banco quais estavam  disponíveis e posso incrementar valor aos mesmos.

Para rodar esse seed, é necessário que você especifique o arquivo, pois pode haver erro se compilar o seed anterior novamente.

```
npx knex seed:run --specific 002_projects.js
```

O comando a baixo serve para fazer um rolback nas migrations

```
npx knex migrate:rollback --all 
```

Desfaz todas as migrations.

---

## Criando Controller da tabela Project

Antes de criar o controller propriamente dito, vamos já incrementar os métodos nas rotas

### Rotas

```js
const express = require('express')
const routes = express.Router()

const UserController = require('./controllers/UserController')
const ProjectController = require('./controllers/ProjectController')

routes
    //Users
    .get('/users', UserController.index)
    .post('/users', UserController.create)
    .put('/users/:id', UserController.update)
    .delete('/users/:id', UserController.delete)

    //Projects
    .get('/projects', ProjectController.index)
    .post('/projects', ProjectController.create)


module.exports = routes
```

> Outro jeito de escrever as rotas de maneira mais sucinta 

### Controller com paginação de Projetos

```js
const knex = require('../database')

module.exports = {
    //Como podem ser as requisições
    //   /projects?user_id=5 ou apenas /projects
    //Está usando paginação, para percorrer para as próximas paginas /projects?page=2
    async index(req,res,next) {
        try {
            const { user_id, page = 1 } = req.query;

            const query = knex('projects')
            .limit(5)
            .offset((page-1) * 5)

            const countProjectUser = knex('projects').count()

            if(user_id){
                query
                    .where({ user_id })
                    .join('users', 'users.id', '=', 'projects.user_id') //Fazendo o Join entre a tabela de users e projects
                    .select('projects.*', 'users.username') // selecionado os valores que deverão ser retornados da tabela

                countProjectUser
                    .where({user_id})
            }

            const [count] = await countProjectUser;
            res.header('X-Total-Count', count["count"])

            const results = await query;

        return res.json(results)
        } catch (error) {
            next(error)
        }
    },

    async create(req,res,next){
        try {
            const { title, user_id} = req.body;

            await knex('projects').insert({
                title,
                user_id
            })

            return res.status(201).send()

        } catch (error) {
            next(error)
        }
    },
}
```

---

## Soft Delete

Uma breve explicação, o soft delete é para não excluirmos necessariamente o projeto, mas atribuir uma coluna delete.

Usaremos o seguinte comando para adicionar a coluna 

```
npx knex migrate:make add_column_delete_at_to_users
```

Após criar o arquivo de migrate, vamos editar ela 

```js
exports.up = knex => knex.schema.alterTable('users', function(table){
    table.timestamp('deleted_at')
})


exports.down = knex => knex.schema.alterTable('users', function(table){
    table.dropColumn('deleted_at')
})
```

Efetivando alterações 

```
npx knex migrate:latest
```

Edite a função delete de UserController

```js
async index(req,res) {
        const results = await knex('users')
            .where('deleted_at',null)

        return res.json(results)
    },
```

&

```js
async delete(req,res){
        try {
            const { id } = req.params
            await knex('users')
                .where({id : id}) //outro exemplo
                .update('deleted_at', new Date())

            return res.send()
        } catch (error) {
            next(error)
        }
    },
```

No Project Controller, acrescente a ultima linha depois do if que valida os projetos contida em async index

```js
if(user_id){
                query
                    .where({ user_id })
                    .join('users', 'users.id', '=', 'projects.user_id') 
    //Fazendo o Join entre a tabela de users e projects
                    .select('projects.*', 'users.username') 
    // selecionado os valores que deverão ser retornados da tabela
                    .where('users.deleted_at',null) //Essa aqui
```

---

## Procedures & Triggers (updated_at)

### Criando uma Trigger ou Procedure

```
npx knex migrate:make add_column_functions
```

Para fazermos uma trigger bem feita, ela deverá ser compilada antes das tabelas já executadas, para isso, renomeie o arquivo no intuito de mostrar ao sistema que ela foi criada antes das outras tabelas e edite o arquivo da seguinte maneira.

```js
const CUSTOM_FUNCTIONS = `
    CREATE OR REPLACE FUNCTION on_update_timestamp()
    RETURNS trigger AS $$
    BEGIN
        NEW.updated_at = now();
        RETURN NEW;
    END;
    $$ language 'plpgsql';
`

const DROP_CUSTOM_FUNCTIONS = `
    DROP FUNCTION on_update_timestamp()
`

exports.up = async knex => {
    return knex.raw(CUSTOM_FUNCTIONS)
}

exports.down = async knex => {
    return knex.raw(DROP_CUSTOM_FUNCTIONS)
}
```

### Função de execução da trigger ou procedure

Agora vá até a raiz do projeto e vamos editar o arquivo knexfile.js 

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
  },
  onUpdateTrigger: table => 
  `
    CREATE TRIGGER ${table}_updated_at
    BEFORE UPDATE ON ${table}
    FOR EACH ROW
    EXECUTE PROCEDURE on_update_timestamp();
  `
};
```

Edite as seguintes migrations:

User Migrations

```js
const { onUpdateTrigger } = require('../../../knexfile')

//utilizando arrow function
exports.up = async knex => knex.schema.createTable('users', function(table){
        table.increments('id')
        table.text('username').unique().notNullable()

        table.timestamp('created_at').defaultTo(knex.fn.now())
        table.timestamp('updated_at').defaultTo(knex.fn.now())
    }).then( () => knex.raw(onUpdateTrigger('users')))


exports.down = async knex => knex.schema.dropTable('users')
```

Project Migration

```js
const { onUpdateTrigger } = require('../../../knexfile')

exports.up = async knex => knex.schema.createTable('projects', function(table){
    table.increments('id')
    table.text('title')

    //relacionamento 1 user pra n projetos
    table.integer('user_id')
        .references('users.id')
        .notNullable()
        .onDelete('CASCADE')

    table.timestamps(true, true)
}).then( () => knex.raw(onUpdateTrigger('projects')))


exports.down = async knex => knex.schema.dropTable('projects')
```

Faça um Rollback para desfazer todas as tabelas e refaça novamente

```
npx knex migrate:rollback --all
```

```
npx knex migrate:latest
```

Faça o teste com seus seeds

```
npx knex seed:run --specific 001_users.js
```

```
npx knex seed:run --specific 002_projects.js
```

### 
