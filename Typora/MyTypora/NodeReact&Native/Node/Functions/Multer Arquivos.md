# Envio de arquivos com o Multer

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

