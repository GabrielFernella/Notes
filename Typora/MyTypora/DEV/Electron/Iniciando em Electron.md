# Iniciando em Electron

## Criando a Aplicação simples

Utilize os seguintes comando para instalar o electron

```
npm init -y
npm install electron --save
```

Além disse crie um arquivo chamado main.js e edite o package.json da seguinte maneira

``` json
{
  "name": "electron_timer",
  "version": "1.0.0",
  "description": "",
  "main": "main.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1",
    "start": "electron ."
  },
     "dependencies": {
    "electron": "^8.2.5"
  }
}
```

- - -

## Primeira Tela

No arquivo Main, podemos preencher da seguinte maneira para fazer um teste

``` js
const { app, BrowserWindow } = require('electron')

app.on('ready', () => {
    let mainWindow = new BrowserWindow({ 
        width:600, //tamanho da tela
        height:400
    })

    mainWindow.loadURL('https://www.alura.com.br') //talvez de para já deixar logado
})
```

Rode o comando para executar o programa

```
npm start
```

- - -

## Montando janelas HTML

O Electron é praticamente um navegador onde vc utilizará sua aplicação, vc pode montar uma estrutura de pastas de um site e utiliza-lo para navegar entre eles.

Crie uma pasta app/ e dentro crie um arquivo um index.html e pastas como css/javascript.

#### Index.html

``` html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Alura Timer</title>
    <link rel="stylesheet" href="css/index.css">
</head>
<body>
    <h1>New window teste</h1>
</body>
</html>
```

#### app/index.css (para um exemplo)

``` css
body {
    background: #cecece;
}
h1 {
    color: blue;
    font-family: Arial, Helvetica, sans-serif;
}
```

#### Main.js

``` js
const { app, BrowserWindow } = require('electron')

app.on('ready', () => {
    let mainWindow = new BrowserWindow({ 
        width:600, //tamanho da tela
        height:400
    })

    mainWindow.loadURL(`file://${__dirname}/app/index.html`)
})

app.on('window-all-closed', () => {
    app.quit();
})
```

Rode a aplicação e vc pode ver uma estrutura simples de html no electron.