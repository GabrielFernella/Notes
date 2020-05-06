# Criando Model e Cadastrando Usuarios

## Criando Model

Dentro da pasta models crie o arquivo 'User.js' (src/app/models) e insira o seguinte código:

```js
import Sequelize,{ Model } from 'sequelize';

class User extends Model {
    static init(sequelize){
        super.init({
            name: Sequelize.STRING,
            email: Sequelize.STRING,
            password: Sequelize.VIRTUAL,
            password_hash: Sequelize.STRING,
            provider: Sequelize.BOOLEAN,
        },
        {
            sequelize,
        }
        );

        return this;
    }
}

export default User;
```

Este é um Model simples, o próximo terá algumas funcionalidades a mais, veja:

```js
import Sequelize,{ Model } from 'sequelize';
import bcrypt from 'bcryptjs';

class User extends Model {
    static init(sequelize){
        //Classe pai extends
        //Colunas que serão inseridas pelo usuário
        //São campos que o usuário pode preencher qunado o usuário utilizar
        super.init({
            name: Sequelize.STRING,
            email: Sequelize.STRING,
            password: Sequelize.VIRTUAL,
            password_hash: Sequelize.STRING,
            provider: Sequelize.BOOLEAN,
        },
        {
            sequelize,
        }
        ); 

        this.addHook('beforeSave', async (user) => { //Criptografia de senhas. pega a senha do campo password, encripta e passa para o password_hash
            if(user.password){
                user.password_hash = await bcrypt.hash(user.password, 8)
            }
        });
        return this;
    }
    static associate(models){
        this.belongsTo(models.File, { foreignKey: 'avatar_id', as:'avatar' });
    }

    checkPassword(password){
        return bcrypt.compare( password, this.password_hash); // this tem acesso a todas as informações da classe, assim, consegue comparar entre os parametros.
        //retorna True || False
    }
}
export default User;
```

Dentro do Model colocamos todos os campos que poderão sofrer alteração do usuário.

---

## Conexão entre o Model e a base de dados

Agora faremos conexão entre o Model e nosso banco de dados, para isso, vamos criar um arquivo na nossa pasta database, chamado index.js (src/database/index.js) e aplicaremos a estrutura do seguinte código, lembrando que é apenas um exemplo com funcionalidades já estabelecidas. portanto, consuma o que for necessário para a sua aplicação.

```js
//Arquivo que fará a conexão com o Banco de dados
import Sequelize from 'sequelize';
import mongoose from 'mongoose';

import User from '../app/models/User';
import File from '../app/models/File';
import Appointment from '../app/models/Appointment';

import databaseConfig from '../config/database';

const models = [User, File, Appointment];

class Database {
    constructor(){
        this.init();
        this.mongo();
    }

    init(){
        this.connection = new Sequelize(databaseConfig);
        models
        .map(model => model.init(this.connection))
        .map(model => model.associate && model.associate(this.connection.models));
    }
    
    mongo(){
        this.mongoConnection = mongoose.connect(
            //'mongodb://localhost:27017/gobarber',
            process.env.MONGO_URL,
            { useNewUrlParser: true,  useFindAndModify: true, useUnifiedTopology:true }
        )}
}

export default new Database();
```

Importe o a configuração da Conexão para o arquivo app.js.

```js
import './database';
```

---

## Cadastrando Usuário pelo Controller

Na pasta controller criaremos o arquivo UserController para manipular as regras de negócio do nosso projeto, localizado em (src/app/controllers) e insira o seguinte código de exemplo inserindo apenas o que será utilizado para a sua aplicação.

Estrutura básica de um controller

```js
import User from '../models/User';

class UserController {
    async store(req, res){
        //...
        return res.json();
    }
}

export default new UserController();
```

Exemplo de um UserController com funcionalidades adicionais:

```js
import * as Yup from 'yup';
import User from '../models/User';

class UserController {
    async store(req, res) {
        //Validação dos dados na requisição
        const schema = Yup.object().shape({
            name: Yup.string().required(),
            email: Yup.string().email().required(),
            password: Yup.string().required().min(8),
        });
        if(!(await schema.isValid(req.body))){
            return res.status(400).json({ error: 'Validation fails'});
        }

        const userExists = await User.findOne({ where: { email: req.body.email }}) //Procura se algum usuário já possui um email
            if(userExists){
                return res.status(400).json({ error: 'User already exists'});
            }
        const {id, name, email, provider} = await User.create(req.body);

        return res.json({
            id,
            name,
            email,
            provider
        });
    }

    async update(req, res){
        const schema = Yup.object().shape({
            name: Yup.string(),
            email: Yup.string().email(),
            oldPassword: Yup.string().min(8),
            password: Yup.string().min(8)
                .when('oldPassword', (oldPassword, field) => oldPassword ? field.required() :  field ),
            confirmPassword: Yup.string().when('passwprd', (password, field) =>
                password ? field.required().oneOf([Yup.ref('password')]) : field )
        });
        if(!(await schema.isValid(req.body))){
            return res.status(400).json({ error: 'Validation fails'});
        }

        const { email, oldPassword } = req.body;

        const user = await User.findByPk(req.userId);

        if(email != user.email){
            const userExists = await User.findOne({ where: { email: req.body.email }}) //Procura se algum usuário já possui um email

            if(userExists){
                return res.status(400).json({ error: 'User already exists'});
            }
        }

        if( oldPassword && !(await user.checkPassword(oldPassword))){
            return res.status(401).json({ error: 'Password does not match'});
        }

        const {id, name, provider} = await user.update(req.body);

        return res.json({
            id,
            name,
            email,
            provider
        });
    }
}

export default new UserController();  
```

---

## Crie a Rota do seu controller

Após criar o controller devemos importar o mesmo no arquivo routes.js (src/routes.js). Deixaremos o seguinte:

```js
import { Router } from 'express';

import UserController from './app/controllers/UserController';

const routes = new Router();

routes.post('/users', UserController.store);

export default routes;
```

Exemplo do arquivo de rotas com diversas rotas e midlewares da aplicação Gobaerber, API desenvolvida que está no GitHub.

```js
import { Router } from 'express'; // Forma de importar o Router para o APP.js

import multer from 'multer';
import multerConfig from './config/multer';

import UserController from './app/controllers/UserController';
import SessionController from './app/controllers/SessionController';
import FileControler from  './app/controllers/FileController';
import ProviderController from  './app/controllers/ProviderController';
import AppointmentController from  './app/controllers/AppointmentController';
import ScheduleController from  './app/controllers/ScheduleController';
import NotificationController from  './app/controllers/NotificationController';
import AvailableController from  './app/controllers/AvailableController';

import authMiddleware from './app/middlewares/auth';

const routes = new Router();
const upload = multer(multerConfig);

routes.post('/users', UserController.store);
routes.post('/sessions', SessionController.store);

routes.use(authMiddleware);
routes.put('/users', UserController.update);

routes.get('/providers', ProviderController.index);
routes.get('/providers/:providerId/available', AvailableController.index);

routes.get('/appointments', AppointmentController.index);
routes.post('/appointments', AppointmentController.store);
routes.delete('/appointments/:id', AppointmentController.delete);

routes.get('/schedule', ScheduleController.index);

routes.get('/notifications', NotificationController.index);
routes.put('/notifications/:id', NotificationController.update);


routes.post('/files', upload.single('file'), FileControler.store);

export default routes; //está sendo executado em app.js
```

---

## Resumo dos Passos

Resumo dos processos necessários para fazer um cadastro simples em uma API

1. Crie um Model para transportar os campos necessários para a tabela na sua base de dados.
2. Faça a conexão entre o model e a base de dados através do arquivo index.js na pasta database.
3. Crie seu Controller e regras de negócios.
4. Importe esse controller para o arquivo de rotas e teste sua aplicação.

Utilize o insomnia para  fazer a requisição a sua API e veja está dando tudo certo. 















