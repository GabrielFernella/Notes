# Bkp dados em Docker

## Trabalhando com volumes

Sabendo disso, vamos ver como trabalhar com o **Docker Host**. No Terminal ou PowerShell (ou Docker Quickstart Terminal), criamos um *container* com o **`docker run`**, mas dessa vez utilizando a *flag* **`-v`** para criar um volume, seguido do nome do mesmo:

```
docker run -v "/var/www" ubuntu
```

No exemplo acima, criamos o volume **/var/www**, mas a que pasta no **Docker Host** ele faz referência? Para descobrir, podemos inspecionar o *container*, executando o comando **`docker inspect`**, passando o seu **id** para o mesmo:

```
docker inspect 8cf7b40ce226
```

Temos uma saída com diversas informações, mas a que nos interessa é o **"Mounts"**:

```
"Mounts": [
    {
        "Type": "volume",
        "Name": "5e1cbfd48d07284680552e56087c9d5196659600ccd6874bfa3831b51ddd0576",
        "Source": "/var/lib/docker/volumes/5e1cbfd48d07284680552e56087c9d5196659600ccd6874bfa3831b51ddd0576/_data",
        "Destination": "/var/www",
        "Driver": "local",
        "Mode": "",
        "RW": true,
        "Propagation": ""
    }
]
```

Nele, podemos ver que o **/var/www** será escrito na nossa máquina no diretório **/var/lib/docker/volumes/5e1cbfd48d07284680552e56087c9d5196659600ccd6874bfa3831b51ddd0576/_data**, endereço que foi gerado automaticamente pelo Docker. Ou seja, tudo que escrevermos na pasta **/var/www** do *container*, na verdade estaremos escrevendo na pasta **/var/lib/docker/volumes/5e1cbfd48d07284680552e56087c9d5196659600ccd6874bfa3831b51ddd0576/_data** da nossa máquina.

E ao remover o *container*, a pasta continuará na nossa máquina. Essa pasta gerada pelo Docker pode ser configurada, podemos dizer a pasta que será referenciada pela pasta **/var/www** do *container*. Por exemplo, se quisermos escrever dentro do Desktop da nossa máquina, devemos passá-lo antes do volume, separando-os com dois pontos. Além disso, vamos executar o *container* no modo interativo:

```
docker run -it -v "C:\Users\Alura\Desktop:/var/www" ubuntu
root@abd0286c0083:/#
```

Ou seja, quando escrevermos na pasta **/var/www** do *container*, estaremos escrevendo no Desktop da nossa máquina. Para provar isso, na pasta **/var/www**, vamos criar um arquivo e escrever nele uma mensagem:

```
root@abd0286c0083:/# cd /var/www/
root@abd0286c0083:/var/www# touch novo-arquivo.txt
root@abd0286c0083:/var/www# echo "Este arquivo foi criado dentro de um volume" > novo-arquivo.txt
```

Ao acessarmos o nosso Desktop, o arquivo estará lá, também com a mensagem escrita. E ao remover o *container*, a sua camada de escrita é removida, mas os arquivos continuam no nosso Desktop.

Então, o uso de volumes é importante para salvarmos os nossos dados fora do *container*, e esses volumes sempre estarão atrelados ao **Docker Host**. No caso acima, atrelamos o volume com o Desktop, mas podemos atrelar com um lugar mais seguro, salvando os dados do banco de dados nele, logs, e até mesmo o código fonte, coisa que faremos no próximo vídeo.