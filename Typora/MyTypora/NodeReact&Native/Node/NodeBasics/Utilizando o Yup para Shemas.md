# Utilizando o Yup para schemas de requisições

Importar o Yup para a pasta onde você utilizará.

```javascript
import * as Yup from 'yup';
```

Utilizando e validando a requisição

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

O Yup compara o Schema com a requisição.