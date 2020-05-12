# Configurando Sucrase

Para configurar o Sucrase vc deve instalar a seguinte dependência 

```
npm install sucrase nodemon -D
```

crie um arquivo na raiz chamado nodemon.json e insira o seguinte código

```json
{
    "execMap": {
        "js": "node -r sucrase/register"
    }
}
```

Seu package.json deve conter o seguinte script

```json
{
  "name": "API-Gobarber",
  "version": "1.0.0",
  "main": "index.js",
  "license": "MIT",
  "scripts":{
    "dev":"nodemon src/server",
    "dev:debug":"nodemon --inspect src/server"
  },
  "dependencies": {
    "express": "^4.17.1"
  },
  "devDependencies": {
    "nodemon": "^2.0.3",
    "sucrase": "^3.14.0"
  }
}
```

Agora vc pode utilizar algumas funcionalidades como import e export 

ex:

```js
import express from 'express';
import routes from'./routes';


export default new App().server
```

