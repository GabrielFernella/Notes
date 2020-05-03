# Configurando os Editores React

Adicionando Eslint como Dependencia de desenvolvimento

```
yarn add eslint -D
```

Para adicionar o arquivo de configuração

```
yarn eslint --init
```

Ordem de opções

```
Eslint --init
```

- 1 - To check syntax, find problems, and enforce code style 
- 2 - JavaScript modules (import/export) 
- 3 - React 
- 4 - No 
- 5 - (*) Browser 
- 6 - Use a popular style guide 
- 7 - Airbnb: https://github.com/airbnb/javascript
- 8 - JavaScript 
- 9 - Y && Y

Instale o prettier e outros pacotes

```
yarn add prettier eslint-config-prettier eslint-plugin-prettier babel-eslint -D
```

Edit eslintrc.js

```jsx
module.exports = {
	env: {
		browser: true,
		es6: true,
	},
	extends: [
		'airbnb',
		'prettier',
		'prettier/react'
	],
	globals: {
		Atomics: 'readonly',
		SharedArrayBuffer: 'readonly',
	},
	parser: 'babel-eslint',
	parserOptions: {
		ecmaFeatures: {
		jsx: true,
		},
		ecmaVersion: 2018,
		sourceType: 'module',
	},
	plugins: [
		'react',
		'prettier'
	],
	rules: {
		//'prettier/prettier': 'error', //confere se as regras batem com os códigos e retorna erro se não retornar true
		'react/jsx-filename-extension': [
			'warn',
			{ extensions: ['.jsx', '.js']}
		],
		'import/prefer-default-export': 'off',
	},
	};
```

Create .prettierrc in raiz and edit

```jsx
{
	"singleQuote": true,
	"trailingComma": "es5"
}
```