# Relacionamento entre tabelas pelo Adonis, App de projetos

## Criando o Model

Para que ocorra tudo corretamente, é necessário que já tenha os models de User e File criados. Para começar iremos criar mais dois models e suas respectivas migrations e Controllers.

```
adonis make:model Project -m -c 
adonis make:model Task -m -c 
```

> Cria a migration e o Controller adonis

---

## Editando Migrations

Agora vamos editar a migration tanto de Project quando de Task para fazer os relacionamentos com as tabela de Users.

Migration Project

```js
'use strict'
const Schema = use('Schema')

class ProjectSchema extends Schema {
  up () {
    this.create('projects', (table) => {
      table.increments()
      table
        .integer('user_id') //nome do campo
        .unsigned() //Força que o resultado seja positivo
        .references('id') //nome do campo que queremos referenciar
        .inTable('users') //Em qual tabela queremos referenciar
        .onUpdate('CASCADE') //Se sofrer alguma alteraçãona tabela de user, essa tabela tbm sofrerá alteração
        .onDelete('SET NULL') //se o usuário for deletado o user_id passa a ser nulo
      table.string('title').notNullable()
      table.text('description').notNullable()
      table.timestamps()
    })
  }

  down () {
    this.drop('projects')
  }
}

module.exports = ProjectSchema
```

Migration Task

```js
'use strict'

const Schema = use('Schema')

class TaskSchema extends Schema {
  up () {
    this.create('tasks', (table) => {
      table.increments()
      table
        .integer('project_id') //nome do campo
        .unsigned() //Força que o resultado seja positivo
        .references('id') //nome do campo que queremos referenciar
        .inTable('users') //Em qual tabela queremos referenciar
        .onUpdate('CASCADE') //Se sofrer alguma alteraçãona tabela de user, essa tabela tbm sofrerá alteração
        .onDelete('CASCADE') //se o PROJETO for deletado, as taferas tbm serão deletadas automaticamente
        .notNullable()
      table
        .integer('user_id')
        .unsigned()
        .references('id')
        .inTable('users')
        .onUpdate('CASCADE')
        .onDelete('SET NULL')
      table
        .integer('file_id')
        .unsigned()
        .references('id')
        .inTable('files')
        .onUpdate('CASCADE')
        .onDelete('SET NULL')
      table.string('title').notNullable()
      table.text('description')
      table.timestamp('due-date') //qual data essa tarefa deverá estar atualizada

      table.timestamps()
    })
  }

  down () {
    this.drop('tasks')
  }
}

module.exports = TaskSchema
```

Após editar as duas migrations, rode o seguinte comando para efetivar a tabela no banco de dados.

```
adonis migration:run
```

---

