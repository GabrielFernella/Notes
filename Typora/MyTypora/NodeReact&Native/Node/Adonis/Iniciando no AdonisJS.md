# Iniciando no AdonisJS

## Os primeiros passos para com o AdonisJS

Esse comando mostra todos os projetos que vc pode gerar com o Adonis (Templates prontos)

```
adonis new -h
```

Comando para criar um projeto adonis, o "- - api-only" é o tipo de pacote simples, mas com tudo que irá precisar

```
adonis new myProject --api-only
```

Antes de rodar o servidor é necessário que vc tenha um banco de dados disponível, no arquivo  config/databases.js o adonis disponibiliza diversos bancos pré-configurados para vc utilizar, basta instalar os drivers do mesmo no seu projeto. Nesse exemplo vamos utilizar o postgres, segue o comando que está nesse mesmo arquivo para poder utiliza-lo.

```
npm i --save pg
```

Depois de ter instalado é necessário que edite o arquivo .env onde estará armazenada as configurações globais da aplicação.

```javascript
DB_CONNECTION=pg
DB_HOST=192.168.99.100
DB_PORT=5432
DB_USER=postgres
DB_PASSWORD=docker
DB_DATABASE=adonis
```

Coloque o servidor ONLINE, e a opção --dev ativa a funcionalidade do "nodemon", se for usar em produção , rode sem o --dev

```javascript
adonis serve --dev
```

Através de outro terminal, faça a migração das migrations pré-criadas pelo adonis, User and Token

```javascript
adonis migration:run
```

Após obter sucesso, o próximo passo é criar o Controller para fazer as requisições, escolha a opção "HTTP" que aparecerá na linha de comand. Uma pasta Controllers irá ser criada na pasta app do projeto.

```javascript
adonis make:controller User
```

Aqui está um exemplo de uma função store no controller

```javascript
  'use strict'

const User = use('App/Models/User')

class UserController { async store({request}) { const data = request.only(['username', 'email', 'password'])
  const user = await User.create(data)

  return user
    }
}

module.exports = UserController
```

Nota-se que o códio é bem mais enxuto comparado ao express.

Depois disso edite o arquivo route.js para criar a Rota

```javascript
'use strict'

const Route = use('Route')

Route.post('users', 'UserController.store')
```

Pronto, vc fez sua primeira inserção de dados na tabela usando AdonisJS.

Caso queira ver todas as Rotas disponíveis na sua aplicação digite esse comando no terminal.

```
  adonis route:list
```

Criando um Token do Usuário, edite o arquivo SessionController depois de executar o comando ( adonis make:controller Session ) e logo após crie a rota.

```javascript
'use strict'

class SessionController {
  async store ({ request, response, auth }) {
    const { email, password } = request.all();

    const token = await auth.attempt(email, password)

    return token
  }
}

module.exports = SessionController
```

- [x] 
