---
date: 2012-04-23 07:48:09
layout: post
slug: tornando-o-uso-do-ssh-mais-simples-e-agradavel
title: Tornando o uso do SSH mais simples e agradável
categories:
- linux
- ssh
---

Há pouco mais de seis meses eu falei aqui sobre [como acessar o GitHub caso a rede onde você se encontra conectado bloqueie acessos à porta 22](http://blog.myhro.info/2011/10/o-firewall-esta-bloqueando-seu-acesso-ao-github/) em servidores externos. A ideia por trás de tal façanha é [utilizar o arquivo "config" do SSH para setar alguns parâmetros padrões](http://nerderati.com/2011/03/simplify-your-life-with-an-ssh-config-file/) (os mais importantes no caso eram a porta e o hostname) que serão aplicados toda vez que você se conectar a um host pré-definido.

Creio que só com o exemplo citado já foi possível perceber o quanto o arquivo "~/.ssh/config" ("%HOMEPATH%\.ssh\config" no Windows), que aqui chamarei apenas de "ssh\_config", pode facilitar a vida de quem utiliza o SSH corriqueiramente. Alguns parâmetros que poderiam ser especificados na própria linha de comando se tornam ainda mais simples se definidos neste arquivo. Vamos começar com alguns exemplos onde esta configuração vem a calhar.

Gosto da possibilidade de acessar meu computador remotamente, principalmente não precisando deixá-lo ligado o tempo todo (o que seria de mim sem o [Wake-On-LAN](http://en.wikipedia.org/wiki/Wake-on-LAN)?) e nem mesmo deixar a [porta necessária para me conectar via RDP aberta](http://www.techworld.com.au/article/418814/leaked_exploit_prompts_researcher_publish_blueprint_critical_rdp_vulnerability/). O que faço é me conectar ao roteador, que nunca é desligado, via SSH e então redirecionar virtualmente uma porta local para acessar o computador mencionado. Parece complicado? Na verdade não é, veja o seguinte comando:

    $ ssh -L <porta-local>:<IP-do-PC-na-LAN>:3389 root@meurouteador.no-ip.org

Pode parecer um pouco confuso quando as variáveis **<porta-local>** e **<IP-do-PC-na-LAN>** são substituídas por um monte de números separados por dois pontos, mas a ideia é bem simples. A **<porta-local>** do computador-cliente que está se conectando ao SSH passará a redirecionar para o **<IP-do-PC-na-LAN>** na porta 3389. Para acessá-la você utilizaria algo como 127.0.0.1:**<porta-local>** (podendo trocar o 127.0.0.1 por "localhost"). Isso tudo sem a necessidade de se abrir mais portas ou o computador que está rodando o servidor RDP ter um IP válido na internet.

Utilizando o ssh\_config, você precisaria de uma entrada mais ou menos assim:

    Host meu-routeador
        HostName meurouteador.no-ip.org
        User root
        LocalForward 3300 192.168.0.1:3389

Se conectaria, trocando aquele enorme comando anterior e evitando diversos erros de digitação que poderiam acontecer, com apenas:

    $ ssh meu-routeador

E poderia acessar o computador remotamente da seguinte forma:

    $ rdesktop localhost:3300

Este processo serve, inclusive, para encapsular protocolos inseguros em cima da camada encriptada criada pelo SSH. Não é o caso do RDP, que já é encriptado nativamente, mas seria interessante caso o protocolo fosse o RFB, o mesmo utilizado pelo [VNC](http://en.wikipedia.org/wiki/Virtual_Network_Computing).

Você pode, por exemplo, especificar qual chave privada será utilizada para se autenticar, caso você utilize várias:

    Host gh
        HostName ssh.github.com
        Port 443
        User git
        IdentityFile ~/.ssh/github.key

Com esta configuração você acaba simplificando comando "clone" do [Git](http://blog.myhro.info/2011/08/git-para-principiantes/) (que aqui no caso está utilizando o SSH para realizar a cópia do repositório). Ao invés de digitar:

    $ git clone git@github.com:usuario/repositorio.git

Você utilizaria:

    $ git clone gh:usuario/repositorio.git

Com isso, você pode notar que as configurações são válidas até mesmo para comandos externos que utilizam o SSH/SCP internamente. No Nautilus, gerenciador de arquivos padrão do Gnome, você não precisaria digitar a senha nem mesmo trocar a porta ao se conectar a um servidor. Tudo seria feito de forma transparente pelo SSH.

Que tal ter um proxy disponível pra lhe socorrer em uma situação de desespero? Utilizando a entrada:

    Host meuproxy
        HostName meuservidor.com
        User meusuario
        Compression yes
        DynamicForward 8080

E se conectando com:

    $ ssh -N meuproxy

Basta colocar seu navegador (ou qualquer outra aplicação que suporte proxy do tipo SOCKS v4 ou v5) para se conectar utilizando o endereço do proxy "localhost" na porta 8080. A opção "Compression" pode lhe economizar alguns bytes, já que na maior parte do tempo você estará transferindo texto puro (e nem todos servidores web ativam compressão via [gzip](https://en.wikipedia.org/wiki/Gzip#Other_uses) por padrão). O parâmetro "-N" é usado para indicar ao SSH que você não quer utilizar um shell remoto e sim apenas estabelecer a conexão. Consulte as man pages "ssh" e "ssh\_config" para conhecer diversas outras opções.

As dicas aqui parecem servir apenas para quem usa Linux, mas funcionam perfeitamente na versão do [OpenSSH](http://www.openssh.com/) (mais conhecido como servidor, mas que na verdade é toda uma suíte de utilitários SSH) que vem com o [Git for Windows](http://msysgit.github.com/) (também conhecido erroneamente como "msysGit"). Aliás, o Git for Windows é meio que um [Cygwin](http://www.cygwin.com/) simplificado (na verdade ele é baseado no [MinGW](http://mingw.org/), que é mais conhecido por possibilitar o uso do [GCC](http://gcc.gnu.org/) no Windows), apenas com os pacotes necessários para a utilização do Git. Sendo assim, você tem um "mini-shell Linux" em sua máquina Windows.

Com isso, gostaria de fazer uma revelação em parte estarrecedora: estou utilizando o Linux (o [Linux Mint](http://linuxmint.com/)) no desktop! Mais especificamente neste exato momento. Sim, é verdade que por [diversas](http://blog.myhro.info/2011/04/dica-do-dia-ufw/) [vezes](http://blog.myhro.info/2011/04/a-verdade-sobre-o-software-livre/) advoguei contra o Linux no desktop (o Windows venceu, conforme-se) mas o estou dando uma nova chance por dois motivos: preciso de um terminal Unix-like de verdade no meu dia-a-dia (e não tenho um Mac disponível atualmente) e tenho como objetivo não utilizar mais softwares piratas, substituindo-os por suas alternativas livres e/ou gratuitas sempre que possível.

Com o Linux atingirei estes dois objetivos muito mais facilmente, além de melhorar mais ainda minha relação com a linha de comandos (se você usa o Linux e os aplicativos que rodam sobre ele somente em modo gráfico, você está fazendo isso errado). Espero postar por aqui qualquer dia desses o relato de como está sendo a experiência, já que de todas as outras vezes que tentei, isto não durou muito tempo.
