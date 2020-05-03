# Node Sequelize Resumos

Ordem de Processos

Vamos levantar os Processos para nosso CRUD Utilizando Sequelize

## Dependencias

```jsx
yarn init -y
yarn add express pg pg-hstore sequelize
yarn add sequelize-cli sucrase nodemon -D
```

## Comandos Sequelize

```jsx
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

## Ordem dos Processos

- Iniciar Projeto 

  ```
  yarn init -y
  ```

- Criar estrutura de pastas:

  ```jsx
  src/
  	server.js
  	routes.js
  	app/
  		models/
  		constollers/
  		middlewares
  	config/
  	database/migrations/ & index.js
  
  .sequelizerc
  nodemon.json
  ```

- Criar config sequelizerc para montar o path dos arquivos do sequelize

  ```jsx
  const { resolve } = require('path');
  
  module.exports = {
      config: resolve(__dirname, 'src', 'config', 'database.js'),
      'models-path': resolve(__dirname, 'src', 'app', 'models'),
      'migrations-path': resolve(__dirname, 'src', 'database', 'migrations'),
      'seeders-path': resolve(__dirname, 'src', 'database', 'seeds'),
  }
  ```

- Criar o arquivo de credenciais do Banco de dados config/database.js

  ```jsx
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

- Crie sua migration e valide para o banco de dados

  ```jsx
  yarn sequelize migration:create --name=create-users
  yarn sequelize db:migrate
  ```

- Crie o Model da tabela criada

- Crie e edite o arquivo de inicialização dos models dentro de database/index.js

  ```jsx
  //Arquivo que fará a conexão com o Banco de dados
  
  import Sequelize from 'sequelize';
  
  import User from '../app/models/User';
  
  import databaseConfig from '../config/database';
  
  const models = [User];
  
  class Database {
      constructor(){
          this.init();
      }
  
      init(){
          this.connection = new Sequelize(databaseConfig);
  
          models
          .map(model => model.init(this.connection))
          .map(model => model.associate && model.associate(this.connection.models));
      }
  	}
  }
  
  export default new Database();
  ```

- Crie um Controller para fazer a inserção de dados na tabela

- Exemplo de API com diversos recursos

  https://github.com/GabrielFernella/Gobarber_API