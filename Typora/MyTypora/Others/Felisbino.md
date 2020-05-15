**1.Descreva algumas das políticas de segurança física das organizações. Expresse-as na forma como poderiam ser implementadas em um sistema de travamento de porta computadorizado.**
A segurança física em empresas e estabelecimentos que utilizam redes de computadores internos é de suma importância pois pode servir como uma porta de acesso para invasores manipularem seu sistema. Através da rede, a pessoa má intencionada pode manipular o sistema de travamento de portas da maneira que desejar, caso o sistema não tenha uma segurança, o indivíduo consegue mandar códigos arbitrários prejudicando a infraestrutura da organização. 



**2.Descreva   algumas   das   maneiras   pelas   quais   o   e-mail   convencional   é   vulnerável   a intromissão, mascaramento, falsificação, reprodução e negação de serviço. Sugira métodos pelos quais o e-mail poderia ser protegido contra cada uma dessas formas de ataque**

- Intromissão, Mascaramento, Falsificação   -  através de uma chave de criptografia gerada em tempos em tempos tanto para o remetente do email quanto o destinatário, para dificultar o acesso as informações da mensagem.
  Aliado também a utilização de certificados digitais, podemos ter um menor possibilidade de mascaramento.
- Reprodução - Mesmo que do dados estejam guardados, uma criptografia mais sofisticada poderia inviabilizar o invasor de ter acesso as mensagens, ou utilizar VPN para o trafego dessas informações.
- Negação de serviço - Recursos de segurança para ataques DDOS e outros  que podem ser evitados através de servidores mais sofisticados.



**3.O PGP é frequentemente usado para comunicação segura por e-mail. Descreva os passos que dois usuários usando PGP devem efetuar, antes que possam trocar mensagens por e-mail com garantias de privacidade e autenticidade.** 
O PGP gera e gerencia chaves públicas e secretas para um usuário. Ele usa criptografia de chave pública RSA para autenticação e para transmitir chaves secretas a um interlocutor e usa os algoritmos de criptografia de chave secreta IDEA ou 3DES para cifrar mensagens de correio eletrônico ou outros documentos.



**4.Explique o papel da criptografia na segurança dos sistemas distribuídos.**

A criptografia é usada para manter o segredo e a integridade da informação, dificultando o acesso a usuários não autorizados. Em sistemas distribuídos, diversas informações sigilosas são trafegadas através das redes e em diversas arquiteturas diferentes, através desse caminho algum usuário pode conseguir interpretar essa mensagem privada e tentar acessar, porém, se a mesma estiver utilizando uma criptografia forte, as possibilidades do invasor ter acesso a essas informações são muito difíceis sem a chave de descriptografia pública ou privada.

**5.Explique   como   funcionam   os   certificados   digitais.   Quais   problemas   de  segurança eles ajudam resolver?** 

Os certificados digitais permitem que seja estabelecida confiança  entre usuários e organização através das suas chaves de autenticação. Com esse certificado, a empresa tem melhores garantias de que o usuário não está burlando algo para ter acesso, sendo que essas as chaves públicas são analisadas e caso haja alguma irregularidade, o usuário não consegue acessar o serviço.

