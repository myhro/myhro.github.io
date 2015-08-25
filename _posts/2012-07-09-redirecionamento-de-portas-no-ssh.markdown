---
date: 2012-07-09 10:28:55
layout: post
title: Redirecionamento de portas no SSH
categories:
- seguranca
- ssh
---

Há poucos meses falei aqui sobre [algumas das facilidades do SSH](http://blog.myhro.info/2012/04/tornando-o-uso-do-ssh-mais-simples-e-agradavel/), citando a possibilidade de se utilizar o recurso de redirecionamento de portas locais para se acessar indiretamente um computador na rede interna, sem deixar esta porta aberta publicamente na internet. Também falei sobre como o [VNC é inseguro e que o NX Server era melhor por rodar em cima do SSH](http://blog.myhro.info/2011/12/acesso-remoto-no-linux-com-o-nx-server/). Porém, aliando alguns destes recursos de tunelamento e redirecionamento de portas do SSH com a simplicidade do VNC, podemos criar uma solução muito interessante para acesso remoto, além de segura, multi-plataforma e muito flexível.

Por padrão, [o VNC escuta nas faixas das portas 5800 e 5900](https://en.wikipedia.org/wiki/Virtual_Network_Computing#Operation). Digo "nas faixas", porque um mesmo servidor VNC pode abrir "n" sessões, cada uma em uma porta, contando a partir da 5900: 5901, 5902, 5903... No caso da 5800 é quase a mesma coisa, com a única diferença sendo o fato de que estas portas são utilizadas no servidor Java, o que permite a um cliente acessar o servidor remotamente via web, sem ter o aplicativo-cliente instalado. O maior problema desta configuração, é que mesmo tendo um sistema de autenticação encriptado, não é muito prudente deixar o servidor VNC disponível abertamente na internet. Algumas implementações do VNC [nem mesmo suportam senhas de mais de 8 caracteres](https://forum.ultravnc.net/viewtopic.php?f=4&t=15459). O mais prudente é considerar que não há segurança suficiente no VNC e deixar isto a cargo do SSH.

Para criar um túnel e acessar um servidor VNC que está instalado na mesma máquina onde está o servidor SSH, a sintaxe do comando para efetuar a conexão a partir do cliente é esta:

    $ ssh -L 5901:localhost:5901 usuario@<ip-do-servidor>

O parâmetro "**-L**", diz que estamos criando uma porta local. Isto é muito importante, pois o que estamos fazendo na verdade é criado um túnel que será usado da seguinte forma: a porta 5901 da sua máquina passará a redirecionar para a porta 5901 do servidor, em sua interface de rede local. Sendo assim, ao se conectar no servidor VNC, você não mais especificaria o endereço "**<ip-do-servidor>:1**" (o VNC pede apenas o número da sessão, não é necessário somar seu valor à porta 5900) e sim utilizaria o endereço "**localhost:1**".

Neste exemplo estamos utilizando a mesma porta em ambos os lados da conexão, mas é importante deixar bem claro que você pode especificar o número que bem entender ao criar uma porta local. Você poderia, por exemplo, fazer com que a porta local 20000 apontasse para a porta 21 do servidor, criando um túnel encriptado para suas transferências de arquivos, e em seguida conectaria seu cliente FTP no endereço "localhost:2000".

No Putty a configuração é tão simples quanto a utilização do cliente SSH via terminal, basta ir em "_Connection -> SSH -> Tunnels_", especificar a porta local (5901) no campo "_Source port_" e o restante (localhost:5901) no campo "_Destination_", em seguida marcando a opção "_Local_" logo abaixo. Ao clicar no botão "_Add_", a porta especificada é adicionada à lista "_Forwarded ports_".

![](/images/2012/putty-local-forward.png)

Esta é a ideia de se redirecionar uma porta local para outra qual o servidor tenha acesso. Acontece que esta porta não precisa ser necessariamente uma porta do servidor, pode ser outra da rede interna, por exemplo. Se o conceito ficar confuso, recomendo a leitura do [post já citado](http://blog.myhro.info/2012/04/tornando-o-uso-do-ssh-mais-simples-e-agradavel/) onde explico como utilizar o conceito para tunelar uma conexão da área de trabalho remota do Windows. A ideia é que o "localhost" especificado poderia ser qualquer outro endereço que o servidor possa se comunicar, além dele mesmo, incluindo um host disponível na internet, uma vez que o objetivo é acessar um outro servidor, passando pelo túnel criado pelo SSH.

As coisas começam a ficar mais interessantes quando nos deparamos com situações como a seguinte: um computador, que se encontra atrás de NAT, seja de um simples routeador caseiro ou dentro da rede de uma grande empresa ou universidade, que possui apenas um IP privado (como 10.0.0.x ou 192.168.0.x), portanto, inacessível através da internet, poderia se beneficiar dos túneis providos pelo SSH para ser acessado externamente? A resposta, como você já deve ter imaginado, é sim! Da mesma forma como abrimos uma porta local para acessar o servidor através do túnel, é possível abrir uma porta remota, para que a partir do servidor possamos acessar o cliente.

Nem sempre é possível utilizar a abordagem de se conectar a um servidor e em seguida acessar, de forma indireta, uma máquina presente na rede interna. E se você não tiver acesso a este servidor? No meu caso, onde o servidor era o meu próprio routeador, o processo foi simples. Mas e se o meu routeador não tivesse um servidor SSH? Eu teria de recorrer a serviços (muito bons) como o [LogMeIn](https://secure.logmein.com/) e [Teamviewer](https://www.teamviewer.com/), que possuem uma abordagem parecida, onde todo o tráfego passa pelos servidores deles, ou simplesmente, utilizar o recurso de porta remota do SSH. A sintaxe é praticamente idêntica à utilizada para se abrir uma porta local:

    $ ssh -R 5901:localhost:5901 usuario@<ip-do-servidor>

Como você pode ver, só muda o parâmetro, que agora passa a ser "-R". No Putty, a mudança é equivalente, bastando escolher a opção "Remote":

![](/images/2012/putty-remote-forward.png)

A partir de agora, qualquer conexão realizada no servidor na porta 5901 passará a redirecionar para a porta 5901 do cliente, enquanto o túnel permanecer ativo. É muito importante salientar que, neste caso, a situação se inverte, pois o "localhost" aqui corresponde ao cliente qual se conectou ao servidor. Isto significa duas coisas diferentes: o servidor não necessariamente estará ouvindo a porta 5901 apenas na interface de rede local, conforme explicarei adiante, e o serviço qual se deseja acessar no cliente não precisa estar disponível na rede interna, muito menos na internet, uma vez que o túnel se refere a sua interface local (127.0.0.1). Leia novamente este parágrafo caso a situação ainda não esteja clara - isto pode lhe poupar muitas dores de cabeça, futuramente, ao se perguntar porque o túnel não está funcionando como deveria.

O detalhe que mais torna a situação dos túneis reversos, estes com a utilização da porta remota, confusos é a questão da visibilidade da porta que foi aberta. Por padrão, ao se realizar a criação deste túnel, o servidor SSH permitirá conexões na porta remota apenas na interface de rede local. É até mesmo uma questão de segurança, uma vez que nem sempre quando a intenção é se conectar a uma máquina atrás de NAT, o desejo é de torná-la disponível globalmente na internet. Você poderia acessá-la apenas a partir do servidor qual estabeleceu a conexão SSH, e não a partir de conexões externas realizadas ao mesmo servidor. É fácil conferir isso com o auxílio do comando "[netstat](http://linux.die.net/man/8/netstat)":

    [root@squeeze:~]# netstat -tap | grep 5901 | tr -s ' '
    tcp 0 0 localhost:5901 *:* LISTEN 2910/4

Os parâmetros "**-tap**" listam todos os sockets TCP abertos em todas as interfaces rede, o "[grep](http://blog.myhro.info/2012/01/expressoes-regulares-grep-egrep-fgrep/)" filtra somente a porta que queremos e o "[tr](http://www.manpagez.com/man/1/tr/)" com o parâmetro "**-s**" remove os espaços desnecessários. Perceba que o servidor está aceitando conexões apenas na interface de rede "localhost" (127.0.0.1). Há duas formas de contornar isto: a forma segura e a não tão segura. A segunda consiste em tornar disponíveis globalmente estes sockets abertos pelo SSH, adicionando a opção "GatewayPorts yes" no arquivo de configuração do servidor SSH (no Debian é o "/etc/ssh/sshd\_config") e em seguida reiniciando-o. Ao realizar novamente a conexão, veja como fica a porta aberta:

    [root@squeeze:~]# netstat -tap | grep 5901 | tr -s ' '
    tcp 0 0 *:5901 *:* LISTEN 2996/4
    tcp6 0 0 [::]:5901 [::]:* LISTEN 2996/4

Agora o socket está disponível não apenas em todas as interfaces, como em ambos os protocolos IPv4 e IPv6. Sendo assim, você poderia, por exemplo, a partir de uma terceira máquina não envolvida no processo de criação do túnel, acessar o VNC disponível na máquina cliente, se conectando ao "**<ip-do-servidor>:1**". Esta possibilidade é muito interessante, não apenas porque possibilita o acesso a um computador antes inacessível via internet, mas pelo fato de que não nos interessa o IP do mesmo. Com um servidor com um IP fixo, você não precisaria se preocupar com diversos clientes com IPs dinâmicos (mutáveis), pois todos estariam acessíveis a partir de um ponto central.

Por último, vamos à forma segura de se acessar esta máquina. Ao invés de tornar a porta disponível na internet, é muito mais seguro, e até mesmo mais simples por não tornar necessária a mudança na configuração do servidor SSH, utilizarmos uma porta local na outra ponta, aliando as duas técnicas de tunelamento. A partir da terceira máquina, basta utilizar o mesmo comando que seria necessário para se conectar a uma sessão do VNC disponível no servidor. Afinal, por mais que o serviço em si não esteja rodando lá, a porta está disponível, e através do túnel podemos acessá-la mesmo se esta estiver restrita a interface local.

    $ ssh -L 5901:localhost:5901 usuario@<ip-do-servidor>

Da mesma forma, a partir do cliente VNC você se conectaria ao endereço "**localhost:1**". A única diferença é que desta vez a conexão passaria por dois túneis:

    terceira-máquina -> servidor
    servidor -> máquina-cliente

Pode não parecer tão prático, mas veja alguns dos problemas que resolvemos e benefícios que obtivemos com esta abordagem.

* A única porta aberta para a internet, levando em conta os três computadores, é a porta do servidor SSH.  
* É possível se conectar a máquinas que antes estariam inacessíveis.  
* Não é necessário se preocupar com o IP volátil das máquinas envolvidas (levando em conta que o servidor tenha um IP fixo ou pelo menos um serviço de DNS dinâmico configurado).  
* Um protocolo inseguro como o VNC foi encapsulado em túneis seguros com a reconhecida criptografia do SSH.  
* Sua única preocupação consiste em estabelecer os túneis e recriá-los em caso de perdas de conexão, o que pode ser feito automaticamente com ferramentas gratuitas como o [Tunnelier](http://www.bitvise.com/tunnelier).

O processo pode não ser tão simples, a princípio, mas a segurança e flexibilidade trazidas podem valer muito mais a pena do que simplesmente "abrir uma porta no modem". A internet hoje é uma selva. Não deixe a segurança de lado apenas porque "nunca aconteceu com você".

Referências:  
[Howto use SSH local and remote port forwarding](http://www.debianadmin.com/howto-use-ssh-local-and-remote-port-forwarding.html)
