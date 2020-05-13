# Utilizando o Yup para schemas de requisições

Utilizamos o Yup para Validação de Schemas.

### Instalar o Yup

```
npm install yup
```

### Importe o Yup para a pasta onde você estará utilizando.

```javascript
import * as Yup from 'yup';
```

### Utilizando e validando a requisição (Simple)

```javascript
const schema = Yup.object().shape({
            name: Yup.string().required(),
            email: Yup.string().email().required(),
            idade: Yup.number().required(),
            peso: Yup.number().required(),
            altura: Yup.number().required(),
        });

        if(!(await schema.isValid(req.body))){
            return res.status(400).json({ error: 'Validation fails'});
        }
```

- Required: Obrigatório
- string/number: Tipo do dado 
- schema.isValid(): verifica se o valor passado é válido retornando Boolean

O Yup compara o Schema com a requisição, assim, utilizando o comando " if ", você pode verificar se é válido todas as requisições ou retornar erro caso não estiver válido.

### Validando Schema cujo falte alguma requisição (Complex)

```js
//Schema Validation
        const schema = Yup.object().shape({
            name: Yup
                .string(),
            email: Yup
                .string()
                .email(),
            oldPassword: Yup
                .string()
                .min(6),
            password: Yup
                .string()
                .min(6)
                .when('oldPassword', (oldPassword, field) => 
                    oldPassword ? field.required() : field 
            //torna obrigatório se o campo oldPassword estiver preenchido
                    ),
            confirmPassword: Yup
                .string()
                .when('password', (password, field) => 
                    password ? field.required().oneOf([Yup.ref('password')]) : field
                ) 
            //oneOF indica que o campo deve ser igual a um dos Array | o Yup.ref carrega a referencia do campo que está utilizando para comparar

                
        });
        //Verifica se o Schema é válido, se não, retorna erro
        if(!(await schema.isValid)){
            return res.status(400).json({error: 'Validations Fails' })
        }
```

Por sua vez, esse exemplo é um pouco mais complexo pois o Yup utiliza de outras requisições para validar determinadas requisições, no corpo do código contém informações melhores sobre a função.