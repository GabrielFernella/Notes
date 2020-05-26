<h1> Configurações de desenvolvimento com TypeScript</h1>

# Config Alura

Instale os pacotes 

```js
npm init -y

npm install typescript --save-dev
```

#### O arquivo tsconfig.json

Precisamos criar o arquivo `alurabank/tsconfig.json` que guardará as configurações do nosso compilador.

```json
{
    "compilerOptions": {
        "target": "es6", //verção do javascript
        "outDir": "app/js",
        "noEmitOnError": true, //Não compila com erro
        "noImplicitAny": true //não atribui Any as variaveis
    },
    "include": [
        "app/ts/**/*"
    ]
}
```

Nele, indicamos em `compilerOptions` as configurações do compilador. No caso, indicamos que o resultado final da compilação será um código compatível com `es6` e que eles ficarão dentro da pasta `app/js`. Por fim, em `include`, indicamos o local onde o compilador deve buscar seus arquivos.

Excelente, temos a configuração mínima para que nosso compilador funcione, mas como o executaremos? Uma boa prática é criarmos um script em nosso `package.json` que se encarregará de chamá-lo para nós através do terminal.

Vamos alterar `alurabank/package.json` e adicionarmos o script:

```
    "compile": "tsc"
```

Nosso package.json ficará assim:

```json
{
  "name": "alurabank",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1",
    "compile":"tsc",
    "start": "tsc -w"
  },
  "author": "",
  "license": "ISC",
  "devDependencies": {
    "typescript": "^2.3.2"
  }
}
```

Feche e abra o VSCode para que ele possa levar em consideração as configurações que realizamos no compilador.

Agora, através do terminal, ainda dentro da pasta `alurabank` faremos:

```js
npm start
```

Após a conclusão do comando, veremos uma série de mensagens de erro, inclusive no próprio visual studio code veremos as mesmas mensagens em todo lugar que estiver sublinhado de vermelho. Isso significa que houve algum problema de compilação do nosso código que precisamos resolver, ou seja, alguma sintaxe não compatível com TypeScript.

----

# Config Rocketseat

