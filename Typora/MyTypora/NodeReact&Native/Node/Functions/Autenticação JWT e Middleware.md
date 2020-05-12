# Criando uma Autenticação JWT através de Middleware

## Instalando extensão JWT e Criando Token

```
npm install jsonwebtoken
```



### Model de usuário

No model de usuário, deixaremos uma função para comparar e checar se o valor do password passado na requisição, bate com os dados que está no nosso banco.

```js
//Tudo que o será alterado através do usuário 
import Sequelize, { Model } from 'sequelize'
import bcrypt from 'bcryptjs';

class User extends Model {
    static init (sequelize){
        super.init({
            name: Sequelize.STRING,
            email: Sequelize.STRING,
            password: Sequelize.VIRTUAL,
            password_hash: Sequelize.STRING,
            provider: Sequelize.BOOLEAN
        },
        {
            sequelize
        }
    );
    this.addHook('beforeSave', async (user) => {
        if(user.password){
            user.password_hash = await bcrypt.hash(user.password, 8);
        }
        }); //ação para criar hash antes de salvar
        return this;
    }

    checkPassword(password){
        return bcrypt.compare(password, this.password_hash)
    }
}

export default User;
```

> A função **checkPassword** compara o valor da senha da requisição com a do banco. 

### Gere um hash aleatório

Entre em algum site que realize uma criptografia de senha para vc utilizar uma frase utilizar como chave de criptografia.

https://www.md5hashgenerator.com/

Agora, crie um arquivo em `src/config/` chamado `auth.js`, esse arquivo é a configuração da sua chave.



### Criando o arquivo de sessão

Dentro da pasta controller do seu projeto, crie um arquivo `SessionController.js`

```js
import jwt from 'jsonwebtoken'; //importação de Módulo
import authConfig from '../../config/auth';

import User from '../models/User';

class SessionController {
    async store(req, res){
        const { email, password } = req.body;

        //find email in your database
        const user = await User.findOne({ where: { email }});
        if(!user){
            return res.status(400).json({ error: 'User not found'})
        }

        if(!(await user.checkPassword(password))){
            return res.status(401).json('Password does not match')
        }

        const { id, name } = user;

        return res.json({
            user: {
                id,
                name,
                email
            },
            token: jwt.sign({ id }, authConfig.secret, 
                            { expiresIn: authConfig.expiresIn })
        })

    }
}

export default new SessionController();
```

Pontos em relação a SessionController

- A condicional checkPassword utiliza a função que está declarada no Model do usuário.
- Passamos o token quando retornamos a reposta para o usuário, utilizamos o método JWT e suas configurações.

Valor passado pelo Insomnia, necessita ter o usuário no banco para retornar OK

```json
{
	"email": "gabs@hotmail.com",
	"password": "12345678"
}
```

---

## Criando Middleware de autenticação

Na pasta src/app/ crie um pasta e arquivo: `middleware/auth.js` onde ficará o middleware de autenticação. Edite esse arquivo da seguinte maneira.

```js
import jwt from 'jsonwebtoken';

import { promisify } from 'util';

import authConfig from '../../config/auth';

export default async (req, res, next) => {
    const authHeader = req.headers.authorization; //Buscando os parametros do header da requisição

    if(!authHeader){ //Caso o parametro não esteja no header da requisição, retorna erro
        return res.status(401).json({ error: 'Token not provided' })
    }

    //dividindo o valor do header que por padão vem "bearer kjsdfvlffvdsljf...", e salvando o token apenas em Token
    const [,token] = authHeader.split(' ');

    try {
        //Essa constante utiliza o promisify, onde eu uso o asyncAwait ao invés de callback, que seria o padrão, e além 
        //disso, ele compara o valor de token com o secret, utilizando o jwt.verify
        const decoded = await promisify(jwt.verify)(token, authConfig.secret);

        //Vamos também incluid o ID do usuário para que a requisição já tenha essa informação
        req.userId = decoded.id;

        return next();
        
    } catch (error) {
        return res.status(401).json({ error:'Token invalid' })
    }
}
```



Rotas com Middleware

Existem duas maneiras de usar o Middleware nas suas rotas, são as seguintes.

```js
import authMiddleware from './app/middlewares/auth';

//Global - Todas as toras após essa, passará por essa autenticação, se for válido, ela prossegue
routes.use(authMiddleware);

//Local - O middleware será executado apenas se essa rota for chamada
routes.put('/users', authMiddleware, UserController.update);
```



