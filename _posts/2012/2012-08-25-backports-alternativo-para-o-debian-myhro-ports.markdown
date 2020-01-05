---
date: 2012-08-25 10:52:18
layout: post
title: 'Backports alternativo para o Debian: Myhro Ports'
...

Há algumas semanas, depois de anos de tentativas, passei a usar o Linux no meu notebook. Desta vez foi diferente, pois não o instalei em dual-boot com o Windows: o desktop qual utilizo todos os dias é apenas o [Debian](http://www.debian.org/). É uma experiência interessante, pois como muitos de vocês sabem, [sempre me posicionei contra o uso do Linux no desktop](http://blog.myhro.info/2011/04/a-verdade-sobre-o-software-livre/), embora o utilize corriqueiramente nos servidores quais administro (que correspondem pelo menos a uma meia dúzia). Porém, minha vontade de abandonar todo e qualquer software pirata (e também evitar de pagar pelas suas versões originais) falou mais alto.

Como não tenho mais idade (nem tempo, diferente da época em que utilizava o Windows 98 e formatava o PC toda semana) para perder um dia inteiro consertando o computador quando alguma atualização de software deu errado, optei por instalar o Debian "[Squeeze](http://wiki.debian.org/DebianSqueeze)" (6.0), dada sua largamente conhecida estabilidade. Como é de se esperar, esta dita estabilidade tem um preço: nem sempre os softwares estão disponíveis em sua última versão ou mesmo todos quais você precisa estão no repositório oficial da distribuição, mesmo com quase 30 mil pacotes presentes por lá. Pensando justamente nestes casos, surgiu o (hoje incorporado como repositório oficial de pacotes) [Debian Backports](http://backports-master.debian.org/).

O propósito do projeto é basicamente portar as versões dos pacotes presentes no repostório "[testing](http://www.debian.org/releases/testing/)" (que corresponde à próxima versão do Debian, hoje apelidada de "[Wheezy](http://wiki.debian.org/DebianWheezy)") para a árvore "[stable](http://www.debian.org/releases/stable/)" (a versão corrente, que atualmente é o já citado "Squeeze"). Tem muita coisa bacana e útil por lá. Foi assim que pude instalar o [Iceweasel](http://www.geticeweasel.org/) ([versão verdadeiramente livre do Firefox](https://en.wikipedia.org/wiki/Mozilla_Corporation_software_rebranded_by_the_Debian_project)) 10.0 (que é a versão "[ESR](http://www.mozilla.org/en-US/firefox/organizations/)", de suporte extendido - a padrão no "Squeeze" é a extremamente velha 3.5, de 2009) e o [LibreOffice](http://www.libreoffice.org/) ([ao invés do zumbi OpenOffice.org](http://br-linux.org/2010/libreoffice-o-fork-comunitario-do-openoffice-pela-document-foundation/) - único disponível sem os backports). O problema é que nem todos os softwares que preciso estão disponíveis por lá - apenas os quais a procura é muito grande.

Para resolver este problema, foi criado o projeto [Myhro Ports](http://blog.myhro.info/ports/), mantido por mim (como vocês poderiam imaginar) e patrocinado pela [Aptans](http://aptans.com/), pois o processo de recompilação dos pacotes pode ser bem demorado e para isso estou utilizando um dos servidores parrudos (pense num Core i7 com 16 GB de RAM) carinhosamente disponibilizado pela empresa. Para utilizá-lo, basta adicionar a seguinte linha no seu "/etc/apt/sources.list":

    deb http://ports.myhro.info/ squeeze-backports main

Adicionar a chave pública do repositório às conhecidas do APT:

    [root@squeeze:~]# wget -q -O - http://ports.myhro.info/myhroports.gpg | apt-key add -

E instalar normalmente o que você precisa com o seu gerenciador preferido, como o [apt-get ou aptitude](http://raphaelhertzog.com/2011/06/20/apt-get-aptitude-%E2%80%A6-pick-the-right-debian-package-manager-for-you/), após atualizar sua lista de pacotes. Cabem ainda algumas observações:

* Estou tentando manter o repositório o mais parecido possível com o repositório de backports oficial: o sufixo dos pacotes segue a linha "~bpo${debian\_release}+${build\_int}", como "~bpo60+1" e a árvore do repositório é a "[squeeze-backports](http://backports-master.debian.org/Contribute/#index5h3)". Não há absolutamente nenhuma modificação nos pacotes além das que se fizerem necessárias para a correta instalação/execução do mesmo na versão atual do Debian.  
* Ao mesmo tempo, o Myhro Ports não é um substituto para o backports oficial e sim um complemento com o propósito de disponibilizar os pacotes que não estão lá disponíveis. Sendo assim, não existirão pacotes redundantes: só está no Myhro Ports o que não existe no Debian Backports.  
* É possível fazer pedidos de pacotes quais você gostaria de utilizar mas não estão disponíveis ainda, seja aqui nos comentários desde post, via tweet ou e-mail (ports arroba myhro ponto info). Lembre-se apenas que o pacote deve estar no repositório "testing". Sem isso, as chances do pacote quebrar a atualização de uma versão do Debian para a outra seriam maiores.  
* Pacotes que tragam grandes modificações, principalmente os que requerem a atualização de bibliotecas essenciais ao sistema, como a libc6, serão evitados.  
* Apenas as arquiteturas [i386](http://www.debian.org/ports/i386/) e [amd64](http://www.debian.org/ports/amd64/) são suportadas atualmente. Como o objetivo do mesmo é portar pacotes voltados para o desktop, isto provavelmente não vai fazer muita diferença.  

Caso você esteja se perguntando o porque de se criar um novo repositório ao invés de contribuir com o backports oficial, a resposta é muito simples: as regras lá são muito mais rígidas. Entretanto, nada impede que no futuro, quem sabe, os repositórios sejam mesclados.
