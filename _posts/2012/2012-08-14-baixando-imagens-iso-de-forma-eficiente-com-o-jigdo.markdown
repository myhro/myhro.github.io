---
date: 2012-08-14 09:26:23
layout: post
title: Baixando imagens ISO de forma eficiente com o jigdo
...

Ter a disposição as imagens mais recentes da distribuição Linux que você usa nem sempre é fácil. Ainda pior do que não ter a ISO que você precisa, é encontrar uma versão mais antiga do que o esperado mofando em algum lugar do HD, sendo necessário aguardar horas até uma nova imagem seja baixada. Distribuições como o [Debian](http://www.debian.org/), por exemplo, liberam novas imagens, com atualizações de segurança da versão estável, [a cada 3-4 meses](http://wiki.debian.org/DebianSqueeze#Release_and_updates), mas você sabia que é possível agilizar o processo de baixar estas novas ISOs?

O [jigdo](https://en.wikipedia.org/wiki/Jigdo) é um software antigo, muito antigo. Sua última versão estável foi lançada em 2006. O mais incrível é que mesmo assim ele ainda funciona - e muito bem. Sua função é basicamente aproveitar todo conteúdo que não mudou entre duas imagens ISO, baixando somente o que for necessário. O mais curioso é que ele não usa nenhum algoritmo sofisticado de transferência eficiente de dados, como o [rsync](https://en.wikipedia.org/wiki/Rsync). O que ele faz é basicamente, analisar um diretório com os arquivos antigos, comparar com a lista dos arquivos da nova imagem e em seguida baixar o que está faltando.

Para começar a usá-lo, você precisa de três coisas: o jigdo em si (o executável "jigdo-lite", que no Debian/Ubuntu é provido pelo pacote [jigdo-file](http://packages.debian.org/squeeze/jigdo-file)), o arquivo ".jigdo" que será responsável por coordenar a transferência dos dados e criação da imagem ISO e o arquivo ".template" que contém as informações necessárias para a realização do processo. Antes que você se pergunte onde encontrar estes arquivos, vamos analisar a estrutura de um [mirror de imagens do Debian](http://ftp.br.debian.org/debian-cd/current/).

Na pasta da versão atual, onde estão disponíveis todas as arquiteturas, você tem a seguinte lista de diretórios, presente em cada uma delas, cujos prefixos são os seguintes:

* "**bt-**": contém apenas arquivos ".torrent", para serem baixados com um cliente [BitTorrent](https://en.wikipedia.org/wiki/BitTorrent) via P2P.  
* "**iso-**": como você pode imaginar, são as pastas que contém as imagens ISO dos discos.  
* "**list-**": contém arquivos compactados cujo conteúdo são as listas de pacotes presentes em cada disco.  
* E por fim, os diretórios que começam com "**jigdo-**" são os que realmente nos interessam, pois estes guardam os arquivos ".jigdo" e seus respectivos ".template".  

Escolhendo a mídia de seu interesse (o sufixo das pastas a representa), o restante é absurdamente simples. Vou tomar como exemplo a imagem da versão Beta 1 do instalador do Debian 7.0 ([Wheezy](http://wiki.debian.org/DebianWheezy), ainda não lançado) cujo qual quero atualizar para a versão mais recente. Primeiramente, monto a imagem em uma pasta qualquer, como a /mnt:

    [myhro@squeeze:~]$ sudo mount -t iso9660 -o loop /home/myhro/debian-wheezy-DI-b1-i386-netinst.iso /mnt

Dentro de uma outra pasta qualquer, baixo os arquivos ".jigdo" e ".template":

    [myhro@squeeze:~/jigdo-tmp]$ wget http://cdimage.debian.org/cdimage/daily-builds/daily/arch-latest/i386/jigdo-cd/debian-testing-i386-netinst.jigdo
    [myhro@squeeze:~/jigdo-tmp]$ wget http://cdimage.debian.org/cdimage/daily-builds/daily/arch-latest/i386/jigdo-cd/debian-testing-i386-netinst.template

E em seguida, executo o próprio jigdo:

    [myhro@squeeze:~/jigdo-tmp]$ jigdo-lite debian-testing-i386-netinst.jigdo
    Jigsaw Download "lite"
    Copyright (C) 2001-2005  |  jigdo@
    Richard Atterer          |  atterer.net
    (...)
    If you already have a previous version of the CD you are
    downloading, jigdo can re-use files on the old CD that are also
    present in the new image, and you do not need to download them
    again. Mount the old CD ROM and enter the path it is mounted under
    (e.g. `/mnt/cdrom').
    Alternatively, just press enter if you want to start downloading
    the remaining files.
    Files to scan:

Este é o momento mais importante, onde o jigdo pede o diretório onde o conteúdo da última imagem baixada foi montado, para que ele possa fazer sua mágica. Indicando a pasta /mnt, o que acontece?

    Files to scan: /mnt
    Not downloading .template file - `debian-testing-i386-netinst.template' already present
    Found 409 of the 572 files required by the template                            
    Copied input files to temporary file `debian-testing-i386-netinst.iso.tmp' - repeat command and supply more files to continue

O jigdo continuará perguntando se você quer procurar os arquivos que ainda estão faltando em outros diretórios, mas apertando apenas "ENTER", ele se oferecerá para baixá-los a partir do mirror configurado no arquivo /etc/apt/sources.list:

    The jigdo file refers to files stored on Debian mirrors. Please
    choose a Debian mirror as follows: Either enter a complete URL
    pointing to a mirror (in the form
    `ftp://ftp.debian.org/debian/'), or enter any regular expression
    for searching through the list of mirrors: Try a two-letter
    country code such as `de', or a country name like `United
    States', or a server name like `sunsite'.
    Debian mirror [http://ftp.br.debian.org/debian/]:

Pressionando "ENTER" novamente, os arquivos serão baixados com o auxílio do [wget](http://www.gnu.org/software/wget/). Ao final, sua ISO é criada de forma exatamente idêntica a presente no mirror, sem você ter baixado nem um terço (neste exemplo) do tamanho total.

    Finished!
    The fact that you got this far is a strong indication that `debian-testing-i386-netinst.iso'
    was generated correctly. I will perform an additional, final check,
    which you can interrupt safely with Ctrl-C if you do not want to wait.
    
    OK: Checksums match, image is good!

Se mesmo assim isto não lhe convencer, você pode sempre [verificar a assinatura da imagem, calculando seu hash manualmente](http://www.hardware.com.br/termos/md5sum). Agora você não tem mais desculpas para manter suas cópias de imagens ISO desatualizadas, muito menos para desperdiçar banda em um tempo onde cada bit ainda é disputado no tapa.
