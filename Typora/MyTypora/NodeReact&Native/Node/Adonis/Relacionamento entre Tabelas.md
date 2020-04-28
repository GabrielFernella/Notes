# Relacionamento entre tabelas pelo Adonis, App de projetos

Para que ocorra tudo corretamente, é necessário que já tenha os models de User e File criados. Para começar iremos criar mais dois models e suas respectivas migrations e Controllers.

```
adonis make:model Project -m -c 
adonis make:model Task -m -c 
```

> Cria a migration e o Controller adonis