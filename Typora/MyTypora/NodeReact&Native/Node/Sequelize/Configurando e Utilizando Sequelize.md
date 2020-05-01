# Configurando e Utilizando o Sequelize

## Estrutura de pastas

Vamos configurar o Sequelize para manipularmos o banco de dados.

Para começarmos, vamos criar uma pasta chamada "config", "database", "app" dentro da pasta "src" do seu projeto. 

As seguintes pastas e arquivos ficaram desse jeito:

```
config/
    database.js
```

```
database/
    migrations/
```

```
app/ 
    models/ 
    controllers/
```

---

## Instalando alguns pacotes

Instale o Sequelize através do seguinte comando no terminal, podendo usar tanto o yarn quanto o npm.

```
npm install sequelize 
```

Adicione também o CLI do sequelize para poder gerar funções pelo terminal. (Executa migrations, cria models e diversas outras funções)

```
npm install sequelize-cli -D
```

---

## configurações do sequelize

Crie um arquivo chamado " .sequelizerc " na raiz do seu projeto e insira o seguinte código:

```js
const { resolve } = require('path');

module.exports = {
    config: resolve(__dirname, 'src', 'config', 'database.js'),
    'models-path': resolve(__dirname, 'src', 'app', 'models'),
    'migrations-path': resolve(__dirname, 'src', 'database', 'migrations'),
    'seeders-path': resolve(__dirname, 'src', 'database', 'migrations'),

}
```

Esse código mostrará ao Sequelize onde estão as pastas que serão manipuladas e salvas as migrations e etc.

---

## Configurações da Database

No nosso exemplo, estamos usando um banco de dados relacionais Postgres, antes de continuar, precisa-se instalar duas dependências para que o sequelize consiga interpretar para o postgres. São elas: 

```
npm install pg pg-hstore
```

Continuando com nossas configurações, no arquivo 'database.js' dentro de 'config/', insira o seguinte código:

```js
module.exports = {
    dialect: 'postgres',
    host: '192.168.99.100',
    username: 'postgres',
    password: 'docker',
    database: 'gobarber',
    define: {
        timestamps: true,
        underscored: true,
        underscoredAll: true,
    }
}
```

Estão todos os dados de conexão com seu banco. Todos os Passos das configurações agora estão terminadas.

---

## Utilizando alguns comandos do CLI do Sequelize

Para criar as migrations pela interface cli, basta inserirmos o seguinte comando no terminal:

```
npx sequelize migration:create --name=create-users
```

Este comando retornará uma estrutura simples de criação da tabela de usuários.

Exemplo de uma tabela já preenchida para criação.

```js
'use strict';

module.exports = {
  up: (queryInterface, Sequelize) => {
    return queryInterface.createTable('users', { 
      id: {
        type: Sequelize.INTEGER,
        allowNull: false,
        autoIncrement: true,
        primaryKey: true,
      },
      name: {
        type: Sequelize.STRING,
        allowNull: false,
      },
      email: {
        type: Sequelize.STRING,
        allowNull: false,
        unique: true,
      },
      password_hash: {
        type: Sequelize.STRING,
        allowNull: false,
      },
      provider: {
        type: Sequelize.BOOLEAN,
        defaultValue: false,
        allowNull: false,
      },
      created_at: {
        type: Sequelize.DATE,
        allowNull: false,
      },
      updated_at: {
        type: Sequelize.DATE,
        allowNull: false,
      },  
    });
  },

  down: (queryInterface) => {
    return queryInterface.dropTable('users');
  }
};
```

---

## Executando a migration

Para validar as informações dessa estrutura de dados e migrar essa tabela para o banco, precisamos executar o seguinte comando no terminal.

```
npx sequelize db:migrate
```

Caso vc pretenda desfazer a última migration execute o seguinte comando: 

```
npx sequelize db:migrate:undo
```

ou para desfazer todas as migrations:

```
npx sequelize db:migrate:undo:all
```

A tabela SequelizeMeta é onde o sequelize armazena suas migrations e o tempo de cada uma delas, assim, ele sempre mantém sua execução linear conforme você cria suas migrations.

Pronto, cheque se a tabela já está criada no seu banco de dados e bora Codar!.
