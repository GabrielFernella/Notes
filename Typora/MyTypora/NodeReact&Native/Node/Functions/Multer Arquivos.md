# Envio de arquivos com o Multer

## Começando a utilizar o Multer

O multer é uma biblioteca que lida com arquivos físicos em sua aplicação 

### Instalar Multer

```
npm install multer
```

### Configurar Multer

Antes de começar a configuração, é necessário que crie uma pasta tmp na raiz do seu projeto. 

Depois disso, vamos criar mais um arquivo dentro de config/ chamada multer.js, onde deixaremos nossas configurações. 

```js
import multer from 'multer';
import crypto from 'crypto';
import { extname, resolve } from 'path';

export default {
    storage: multer.diskStorage({
        destination: resolve(__dirname, '..', '..', 'tmp', 'uploads'), //local onde será armazenado o arquivo
        filename: (req, file, cb ) => {
            crypto.randomBytes(16, (err, res)=> {
                if(err) return cb(err);
                //esse return, retorna o nome do aquico já codificado para hexa.extenção
                return cb(null, res.toString('hex') + extname(file.originalname));
            })
        }
    })
}
```

Agora adicionaremos os importes para nossa rota poder salvar os arquivos da chamada.

No arquivo de route.js adicione o seguinte:

```js
import multer from 'multer';
import multerConfig from './config/multer'

const upload = multer(multerConfig);


routes.post('/files', upload.single('file'), (req, res) => {
    return res.json({ ok: true })
})
```

---

## Referenciando os arquivos

### Criando Tabela Files

Criando a tabela para realizar a referência entre o usuário e seu avatar(arquivo)

```
npx sequelize migration:create --name=create-files
```

Deixe o arquivo da seguinte Maneira

```js
'use strict';
module.exports = {
  up: (queryInterface, Sequelize) => {
    return queryInterface.createTable('files', { 
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
      path: {
        type: Sequelize.STRING,
        allowNull: false,
        unique: true,
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
    return queryInterface.dropTable('files');
  }
};

```

Para executar a migration para o Banco de Dados

```
npx sequelize  db:migrate
```

### Crie o Model da tabela Files

```js
import Sequelize, { Model } from 'sequelize';

class File extends Model {
    static init (sequelize){
        super.init({
            name: Sequelize.STRING,
            path: Sequelize.STRING,
        },
        {
            sequelize
        });
        return this;
    }
}

export default File;
```

