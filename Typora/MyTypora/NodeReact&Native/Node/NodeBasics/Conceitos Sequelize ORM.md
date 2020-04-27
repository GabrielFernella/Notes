# Conceitos do Sequelize ORM e MVC

## Características do Sequelize

Sequelize: ORM para banco de dados SQL, ORM é uma de abstração do banco de dados onde tabelas viram models para que  seja utilizando entre tipos de bancos diferentes.

> Tabela viram Models manipulados por Javascript

Migration

- Controle de versão para a base de dados
- Cada arquivo contém instrução para criação, alteração ou remoção de tabelas ou colunas
- Mantém a base de dados atualizadas entre os devs e é ordenada por data
- Cada migration deve realizar alterações em apenas uma tabela

Seeds 

- Populam a base de dados para testes
- Utilizado para testes
- Jamais utilizados em produção

---

## Arquitetura MVC

- Model: Armazena a abstração do banco, utilizado para manipular os dados contidos nas tabelas do banco. Não é responsável pela regra de negócio.

- Controller: Ponto de entrada das requisições da aplicação, uma rota está associada diretamente com um método controller. parte as regras de negócio da aplicação.

- View: Retorno ao cliente, retorno json para o frontend poder manipular da melhor maneira.

### Faces de um Controller

- Classes
- Sempre retorna um json
- Não chama outro controller/método              
- Quando criar um novo controller, apenas 5 métodos, precisa ser da mesma entidade
- Métodos: index, show, store, update, delete              

---

## Autenticação JWT

A autenticação JWT ela é feita através de Token, e pode ser aplicada em controllers ou em Middlewares para autenticar o usuário que está fazendo as permitidas requisições.

Características das Assinaturas:

- headers: cabeçalho, tipo do algoritmo

- Payload: Dados Adicionais

- Assinatura: Garante que o token não foi alterado por algum usuário
