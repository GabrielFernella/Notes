# NodeJS e Sequelize



## Ordem de Processos

1. Configurar ambiente

2. Instalar dependências e montar estrutura (subrase, sequelize e etc)

3. config database

4. montar migration

5. criar model

6. Criar o Loader de Model / Editar

7. Criar Controller
8. add route

Vamos levantar os Processos para nosso CRUD Utilizando Sequelize

## Dependências

```jsx
npm init -y
npm install express pg pg-hstore sequelize
npm install sequelize-cli nodemon sucrase -D
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

---

## Criar Projeto e Instalar dependências

Crie seu Projeto

```jsx
yarn init -y
```

Depois de criar seu projeto pelo comando abaixo vc deverá instalar as dependências para que seu package.json fica dessa maneira.

```jsx
{
  "name": "API-Gobarber",
  "version": "1.0.0",
  "main": "index.js",
  "license": "MIT",
  "scripts": {
    "dev": "nodemon src/server",
    "dev:debug": "nodemon --inspect src/server"
  },
  "dependencies": {
    "express": "^4.17.1",
    "pg": "^8.1.0",
    "pg-hstore": "^2.3.3",
    "sequelize": "^5.21.8"
  },
  "devDependencies": {
    "nodemon": "^2.0.3",
    "sequelize-cli": "^5.5.1",
    "sucrase": "^3.14.0"
  }
}
```

Na Raiz do projeto, crie um arquivo chamado `nodemon.json`, insira o seguinte código para configurar o sucrase

```json
{
    "execMap": {
        "js": "node -r sucrase/register"
    }
}
```



---

## Arquivos de configuração

Dentro de src, crie os seguintes arquivos

```jsx
server.js
routes.js
app.js
config/database.js    //onde ficara as configurações do projeto 
database/migrations & index.js // pasta relacionada a conexão com o banco de dados
```

Abaixo estará com os arquivos já preenchidos

### app.js

```js
import express from 'express';
import routes from'./routes';

import './database';

class App {
    constructor(){
        this.server = express();

        this.middlewares()
        this.routes()
    }

    middlewares(){
        this.server.use(express.json());
    }

    routes(){
        this.server.use(routes)
    }
}

export default new App().server
```

### 

### server.js

```jsx
import app from'./app';

app.listen(3333)
```

### routes.js

```jsx
import { Router } from'express'; //import feito pelo sucrase

import UserController from './app/controllers/UserController';

const routes = new Router();

routes.post('/users', UserController.store);


export default routes;
```

### config/database.js

Configurações das credenciar e conexão com o banco de dados

```jsx
module.exports = {
  dialect: 'postgres',
  host: 'localhost',
  username: 'docker',
  password: 'docker',
  database: 'sqlnode',
  define: {
    timestamps: true, //cria o created_at e updated_at automaticamente
    underscored: true,
    underscoredAll: true,
  },
};
```

### database/index.js

```jsx
import Sequelize from 'sequelize';

import databaseConfig from '../config/database';

import User from '../app/models/User';

const models = [User];

class Database {
    constructor(){
        this.init();
    }

    init(){
        this.connection = new Sequelize(databaseConfig)

        models.map(model => model.init(this.connection));
    }
}


export default new Database();

/////////////////////OU

const Sequelize = require('sequelize');
const dbConfig = require('../config/database'); //importando as credenciais 

const User = require('../models/User'); 

const connection = new Sequelize(dbConfig); //utilizando as credenciais em uma função padrão

User.init(connection);

User.associate(connection.models);

module.exports = connection;
```

---

## Arquivo de configuração do sequelize

Antes de prosseguir, o sequelize precisa entender onde estão os arquivos de configurações do sequelize, para isso, criamos um arquivo na raiz do projeto chamado: " .sequelizerc" e preenchemos ele da seguinte maneira.

```jsx
const { resolve } = require('path');

module.exports = {
    config: resolve(__dirname, 'src', 'config', 'database.js'),
    'models-path': resolve(__dirname, 'src', 'app', 'models'),
    'migrations-path': resolve(__dirname, 'src', 'database', 'migrations'),
    'seeders-path': resolve(__dirname, 'src', 'database', 'seeds'),
}
```

Após fazer todos os processos acima, ative o banco de dados docker e rode o seguinte comando:

```jsx
yarn sequelize db:create
```

---

## Criando a Primeira Migration

Para criar uma migration pelo sequelize-cli, basta executar esse comando no terminal

```jsx
yarn sequelize migration:create --name=create-users
```

O comando acima criará um template de migration e logo após deixarei um exemplo de como ela pode ser editada.

```jsx
'use strict';

module.exports = {
  up: (queryInterface, Sequelize) => {
    return queryInterface.createTable('users', { 
      id: {
        type: Sequelize.INTEGER,
        primaryKey: true,
        autoIncrement: true,
        allowNull: false,
      },
      name: {
        type: Sequelize.STRING,
        allowNull: false,
      },
      email: {
        type: Sequelize.STRING,
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

  down: (queryInterface, Sequelize) => {
    return queryInterface.dropTable('users');
  }
};
```

Agora para efetivar esse schema para o banco de dados, execute o seguinte comando

```jsx
yarn sequelize db:migrate
```

---

## Model

Para fazer a primeira parte de inserção de registros precisamos criar nosso model, que é uma representação de como nossa aplicação irá se comunicar com a base de dados.

Para tal crie o seguinte arquivo dentro de src/models/User.js

```jsx
//Tudo que o será alterado através do usuário 
import Sequelize, { Model } from 'sequelize'

class User extends Model {
    static init (sequelize){
        super.init({
            name: Sequelize.STRING,
            email: Sequelize.STRING,
            password_hash: Sequelize.STRING,
            provider: Sequelize.BOOLEAN
        },
        {
            sequelize
        }
    );
    }
}

export default User;
/////////OU
const { Model, DataTypes } = require('sequelize');

class User extends Model {
  static init(sequelize) { //método que recebe a conexão com o sequelize
    super.init({
      name: DataTypes.STRING,
      email: DataTypes.STRING,
    }, {
      sequelize
    })
  }
}

module.exports = User;
```

No arquivo index, dentro da pasta database, importamos o model para comprarar com o banco de dados, nessa parte, apenas para explicar.

EX:

```jsx
const User = require('../models/User');  //importar o model de user

const connection = new Sequelize(dbConfig); //utilizando as credenciais em uma função padrão

User.init(connection); // passando a conexão para o metodo init do Model

User.associate(connection.models);
```

---

## Criando o Controller

Na pasta src, criaremos um arquivo dentro de controller, com seguinte caminho: src/controller/UserController.js

```jsx
import User from '../models/User';

class UserController {
    async store(req,res){
        const userExists = await User.findOne({ where: { email: req.body.email }})
        if(userExists){
            return res.status(400).json({error: 'User alredy exists' })
        }

        const {id, name, email,provider} = await User.create(req.body)
        
        return res.json({
            id,name,email,provider
        });
    }
}
export default new UserController();
//OU /////////////////////////////////////////////////////
const User = require('../models/User');

module.exports = {
  async index(req, res) {
    const users = await User.findAll();

    return res.json(users);
  },

  async store(req, res) {
    const { name, email } = req.body;

    const user = await User.create({ name, email });

    return res.json(user);
  }
};
```

Nos controllers ficam todos os métodos que estão em routes, lembre-se de atualizar o arquivo de rotas da sua aplicação e testar usando o insominia.

---

## Relacionamentos entre tabelas

### Migration com chave estrangeira

Para esse exemplo utilizaremos o modelo 1 ... N, ou seja , um usuário pode ter N endereços e um endereço pertence apenas a 1 usuário.

Começaremos com o seguinte comando para criar nossa nova migration

```jsx
yarn sequelize migration:create --name=create-addresses
```

Teremos que fazer nossa nova tabela, a migration a seguir está com um campo a mais para identificar o usuário pelo user_id, que será a chave estrangeira dessa tabela.

```jsx
'use strict';

module.exports = {
  up: (queryInterface, Sequelize) => {
    return queryInterface.createTable('addresses', { 
      id: {
        type: Sequelize.INTEGER,
        primaryKey: true,
        autoIncrement: true,
        allowNull: false,
      },
      user_id: {
        type: Sequelize.INTEGER,
        allowNull: false,
                //referencia a tabela users o campo id (Chave estrangeira)
        references: { model: 'users', key: 'id' }, 
        onUpdate: 'CASCADE',
        onDelete: 'CASCADE',
                //Caso seja feita alguma alteração, esses campos tbm sofrerão a alteração
                //Exemplo: se um usuário for deletado, os endereços tbm serão deletado
      },
      zipcode: {
        type: Sequelize.STRING,
        allowNull: false,
      },
      street: {
        type: Sequelize.STRING,
        allowNull: false,
      },
      number: {
        type: Sequelize.INTEGER,
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

  down: (queryInterface, Sequelize) => {
    return queryInterface.dropTable('addresses');
  }
};
```

Efetive a migration

```jsx
yarn sequelize db:migrate
```

### Model Chave estrangeira

Continuando, criamos um novo model na pasta models, chamadado Address.js

```jsx
const { Model, DataTypes } = require('sequelize');

class Address extends Model {
  static init(sequelize) {
    super.init({
      zipcode: DataTypes.STRING,
      street: DataTypes.STRING,
      number: DataTypes.INTEGER,
    }, {
      sequelize
    })
  }

    //Relacionamento no Model, o usuário tbm precisa ter essa associação
  static associate(models) {
    this.belongsTo(models.User, { foreignKey: 'user_id', as: 'user' });
  }
}

module.exports = Address;
```

Adicione o model a database e crie suas respectivas rotas e controllers

Rota diferenciada, pois vc passará o id do usuário pelos parâmetros da requisição

```jsx
routes.post('/users/:user_id/addresses', AddressController.store)
```

Em controllers, terá algumas diferenças

```jsx
const User = require('../models/User');
const Address = require('../models/Address');

module.exports = {
  async index(req, res) {
    const { user_id } = req.params;

    const user = await User.findByPk(user_id, {
      include: { association: 'addresses' }
    });

    return res.json(user.addresses);
  },

  async store(req, res) {
    const { user_id } = req.params;
    const { zipcode, street, number } = req.body;

    const user = await User.findByPk(user_id);

    if (!user) {
      return res.status(400).json({ error: 'User not found' });
    }

    const address = await Address.create({
      zipcode,
      street,
      number,
      user_id,
    });

    return res.json(address);
  }
};
```

Estando tuso certo até aqui, temos que associar as chaves estrangeiras ao index da nossa database, ficando da seguinte maneira

```jsx
const Sequelize = require('sequelize');
const dbConfig = require('../config/database');

const User = require('../models/User');
const Address = require('../models/Address');
const Tech = require('../models/Tech');

const connection = new Sequelize(dbConfig);

User.init(connection);
Address.init(connection);
Tech.init(connection);

User.associate(connection.models);
Address.associate(connection.models);
Tech.associate(connection.models);

module.exports = connection;
```

Adicione essa linha dento de Model User, assim como o Model de Address, o usuário tbm precisa conter essa associação

```jsx
static associate(models) {
    this.hasMany(models.Address, { foreignKey: 'user_id', as: 'addresses' });
    this.belongsToMany(models.Tech, { foreignKey: 'user_id', through: 'user_techs', as: 'techs' });
  }
```

[GitHub](https://github.com/Rocketseat/masterclass-nodejs-sql)

[YouTube](https://www.youtube.com/watch?v=Fbu7z5dXcRs&list=PL85ITvJ7FLoiNndfuEs2So-MFLSMvBmmD&index=9)


