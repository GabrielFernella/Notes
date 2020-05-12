# Fazendo o Hash das senhas 

### Instalando bcryptjs

```
npm install bcryptjs
```

### Arquivos que usarão o hash de senhas 

Para utilizarmos o criptografia de senhas, precisamos editar nosso Model, deixando da seguinte maneira:

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
}

export default User;
```

- Na linha 3 estamos importando o bcryptjs que acabamos de instalar.
- Os atributos do nosso model contém o campo passsword, que não será armazenado no banco de dados e sim utilizaremos o mesmo para pegar o valor, fazer o hash e transferir a criptografia para o campo password_hash do banco de dados.
- O Hook serve justamente para fazer essa criptografia antes do campo ser editado (beforeSave), sendo assim, a senha que está sendo passada pela requisição, passa pelo hook que criptografa e em seguida passa para nossa password_hash da tabela User.

