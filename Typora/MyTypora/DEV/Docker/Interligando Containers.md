# Networking no Docker

## Redes com Docker

A boa notícia é que no Docker, por padrão, já existe uma ***default network\***. Isso significa que, quando criamos os nossos *containers*, por padrão eles funcionam na mesma rede:

Para verificar isso, vamos subir um *container* com Ubuntu:

```
docker run -it ubuntu
```

Em outro terminal, vamos verificar o **id** desse *container* através do comando **`docker ps`**, e com ele em mãos, vamos passá-lo para o comando **`docker inspect`**. Na saída desse comando, em ***NetworkSettings\***, vemos que o *container* está na rede padrão ***bridge\***, rede em que ficam todos os *containers* que criamos.

Voltando ao terminal do *container*, se executarmos o comando **`hostname -i`** vemos o IP atribuído a ele pela rede local do Docker:

```
root@973feeeeb1df:/# hostname -i
172.17.0.2
```

Então, dentro dessa rede local, os *containers* podem se comunicar através desses IPs. Para comprovar isso, vamos deixar esse *container* rodando e criar um novo:

```
docker run -it ubuntu
```

E vamos verificar o seu IP:

```
root@dd316a9f585f:/# hostname -i
172.17.0.3
```

Agora, no primeiro *container*, vamos instalar o pacote **iputils-ping** para podermos executar o comando **`ping`** para verificar a comunicação entre os *containers*:

```
root@973feeeeb1df:/# apt-get update && apt-get install iputils-ping
```

Após o término da instalação, executamos o comando **`ping`**, passando para ele o IP do segundo *container*. Para interromper o comando, utilizamos o atalho **CTRL + C**:

```
root@973feeeeb1df:/# ping 172.17.0.3
PING 172.17.0.3 (172.17.0.3) 56(84) bytes of data.
64 bytes from 172.17.0.3: icmp_seq=1 ttl=64 time=0.180 ms
64 bytes from 172.17.0.3: icmp_seq=2 ttl=64 time=0.133 ms
64 bytes from 172.17.0.3: icmp_seq=3 ttl=64 time=0.148 ms
^C
--- 172.17.0.3 ping statistics ---
3 packets transmitted, 3 received, 0% packet loss, time 1999ms
rtt min/avg/max/mdev = 0.133/0.153/0.180/0.024 ms
```

Assim, podemos ver que os *containers* estão conseguindo se comunicar entre eles.

## Comunicação entre containers utilizando os seus nomes

Então, o Docker criar uma rede virtual, em que todos os *containers* fazem parte dela, com os IPs automaticamente atribuídos. Mas quando os IPs são atribuídos, cada hora em que subirmos um *container*, ele irá receber um IP novo, que será determinado pelo Docker. Logo, se não sabemos qual o IP que será atribuído, isso não é muito útil quando queremos fazer a comunicação entre os *containers*. Por exemplo, podemos querer colocar dentro do aplicativo o endereço exato do banco de dados, e para saber exatamente o endereço do banco de dados, devemos configurar um nome para aquele *container*.

Mas nomear um *container* nós já sabemos, basta adicionar o **`--name`**, passando o nome que queremos na hora da criação do *container*, certo? Apesar de conseguirmos dar um nome a um *container*, a rede do Docker não permite com que atribuamos um ***hostname\*** a um *container*, diferentemente de quando criamos a nossa própria rede.

Na rede padrão do Docker, só podemos realizar a comunicação utilizando IPs, mas se criarmos a nossa própria rede, podemos "batizar" os nossos *containers*, e realizar a comunicação entre eles utilizando os seus nomes:

Isso não pode ser feito na rede padrão do Docker, somente quando criamos a nossa própria rede.

## Criando a nossa própria rede do Docker

Então, vamos criar a nossa própria rede, através do comando **`docker network create`**, mas não é só isso, para esse comando também precisamos dizer qual *driver* vamos utilizar. Para o padrão que vimos, de ter uma nuvem e os *containers* compartilhando a rede, devemos utilizar o *driver* de ***bridge\***.

Especificamos o driver através do **`--driver`** e após isso nós dizemos o nome da rede. Um exemplo do comando é o seguinte:

```
docker network create --driver bridge minha-rede
```

Agora, quando criamos um *container*, ao invés de deixarmos ele ser associado à rede padrão do Docker, atrelamos à rede que acabamos de criar, através da *flag* **`--network`**. Vamos aproveitar e nomear o *container*:

```
docker run -it --name meu-container-de-ubuntu --network minha-rede ubuntu
```

Agora, se executarmos o comando **`docker inspect meu-container-de-ubuntu`**, podemos ver em ***NetworkSettings\*** o *container* está na rede **minha-rede**. E para testar a comunicação entre os *containers* na nossa rede, vamos abrir outro terminal e criar um segundo *container*:

```
docker run -it --name segundo-ubuntu --network minha-rede ubuntu
```

Agora, no **segundo-ubuntu**, instalamos o **ping** e testamos a comunicação com o **meu-container-de-ubuntu**:

```
root@00f93075d079:/# ping meu-container-de-ubuntu
PING meu-container-de-ubuntu (172.18.0.2) 56(84) bytes of data.
64 bytes from meu-container-de-ubuntu.minha-rede (172.18.0.2): icmp_seq=1 ttl=64 time=0.210 ms
64 bytes from meu-container-de-ubuntu.minha-rede (172.18.0.2): icmp_seq=2 ttl=64 time=0.148 ms
64 bytes from meu-container-de-ubuntu.minha-rede (172.18.0.2): icmp_seq=3 ttl=64 time=0.138 ms
^C
--- meu-container-de-ubuntu ping statistics ---
3 packets transmitted, 3 received, 0% packet loss, time 2000ms
rtt min/avg/max/mdev = 0.138/0.165/0.210/0.033 ms
```

Conseguimos realizar a comunicação entre os *containers* utilizando somente os seus nomes. É como se o **Docker Host**, o ambiente que está rodando os *containers*, criasse uma rede local chamada **minha-rede**, e o nome do *container* será utilizado como se fosse um *hostname*.

Mas lembrando que só conseguimos fazer isso em redes próprias, redes que criamos, isso não é possível na rede padrão dos *containers*.



---

## Exemplo

Para praticar o que vimos sobre redes no Docker, vamos criar uma pequena aplicação que se conectará ao banco de dados, utilizando tudo o que vimos no vídeo anterior.

O que vamos fazer é utilizar a aplicação **alura-books**, que irá pegar os dados de um banco de dados de livros e exibi-los em uma página web. É uma aplicação feita em Node.js e o banco de dados é o MongoDB.

## Pegando dados de um banco em um outro container

Então, primeiramente vamos baixar essas duas imagens, a imagem **douglasq/alura-books** na versão **cap05** e a imagem **mongo**:

```
docker pull douglasq/alura-books:cap05
docker pull mongo
```

Na imagem **douglasq/alura-books**, não há muito mistério. Ela possui o arquivo **server.js**, que carrega algumas dependências e módulos que são instalados no momento em que rodamos a imagem. Esse arquivo carrega também as configurações do banco, que diz onde o banco de dados estará em execução, no caso o seu *host* será **meu-mongo**, e o *database*, com nome de **alura-books**. Então, quando formos rodar o *container* de MongoDB, seu nome deverá ser **meu-mongo**. Além disso, o arquivo realiza a conexão com o banco, configura a porta que será utilizada (**3000**) e levanta o servidor .

No **Dockerfile** da imagem, também não há mistério, é basicamente o que vimos no vídeo anterior. Por fim, temos as **rotas**, que são duas: a rota **`/`**, que carrega os livros e os exibe na página, e a rota **`/seed`**, que salva os livros no banco de dados.

> *Caso queira, você pode baixar [aqui](https://s3.amazonaws.com/caelum-online-public/646-docker/05/projetos/alura-docker-cap05.zip) o código da versão **cap05** da imagem **alura-books**.*

Visto isso, já podemos subir a imagem:

```
docker run -d -p 8080:3000 douglasq/alura-books:cap05
```

Ao acessar a página **http://localhost:8080/**, nenhum livro nos é exibido, pois além de não termos levantado o banco de dados, nós não salvamos nenhum dado nele. Então, vamos excluir esse *container* e subir o *container* do MongoDB, lembrando que o seu nome deve ser **meu-mongo**, e vamos colocá-lo na rede que criamos no vídeo anterior:

```
docker run -d --name meu-mongo --network minha-rede mongo
```

Com o banco de dados rodando, podemos subir a aplicação do mesmo jeito que fizemos anteriormente, mas **não podemos nos esquecer que ele deve estar na mesma rede do banco de dados**, logo vamos configurar isso também:

```
docker run --network minha-rede -d -p 8080:3000 douglasq/alura-books:cap05
```

Agora, acessamos a página **http://localhost:8080/seed/** para salvar os livros no banco de dados. Após isso, acessamos a página **http://localhost:8080/** e vemos os dados livros são extraídos do banco e são exibidos na página. Para provar isso, podemos parar a execução do **meu-mongo** e atualizar a página, veremos que nenhum livro mais será exibido.

Então, esse foi um exemplo para praticar a comunicação entre *containers*, sempre lembrando que devemos colocá-los na mesma rede. Na próxima aula, veremos um jeito de orquestrar melhor diversos *containers* e automatizar esse processo de levantá-los e configurá-los, ao invés de fazer tudo na mão.

---

No último video usei o comando `docker pull douglasq/alura-books:cap05` sem ter explicado antes. Fique tranquilo pois esse comando faz nada mais do que baixar, *sem rodar*, a imagem desejada do Docker Hub :)

Por exemplo, para baixar a imagem `ubuntu` do Docker Hub você pode usar:

```
docker pull ubuntu
```

Isso é diferente do comando `run`, que baixa a imagem (se não existe local) e depois cria e roda o container. O `pull` apenas baixa!

Para baixar uma imagem de um usuário especifico vimos a sintaxe:

```
docker pull NOME_USUARIO/NOME_IMAGEM
```

Além disso, uma imagem pode ter um **tag** que serve para pegar uma determinada versão dessa imagem. Alias, você já viu a tag `:latest` e no video eu usei a tag `:cap05`, por exemplo:

```
docker pull douglasq/alura-books:cap05
```

Isso baixará a versão (ou tag) `cap05` da imagem `alura-books` do usuário `douglasq`.



Neste capítulo aprendemos:

- Que imagens criadas pelo Docker acessam a mesma rede, porém apenas através de IP.
- Ao criar uma rede é possível realizar a comunicação entre os containers através do seu nome.
- Que durante a criação de uma rede precisamos indicar qual driver utilizaremos, geralmente, o driver `bridge`.

Segue também uma breve lista dos comandos utilizados:

- `hostname -i` **-** mostra o ip atribuído ao container pelo docker (funciona apenas dentro do container).
- `docker network create --driver bridge NOME_DA_REDE` **-** cria uma rede especificando o driver desejado.
- `docker run -it --name NOME_CONTAINER --network NOME_DA_REDE NOME_IMAGEM` **-** cria um container especificando seu nome e qual rede deverá ser usada.