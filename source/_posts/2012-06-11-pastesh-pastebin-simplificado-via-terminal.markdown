---
date: 2012-06-11 09:49:00
layout: post
title: 'PasteSH: pastebin simplificado, via terminal'
categories:
- pastebin
- terminal
---

O formato dos diversos [pastebins](http://en.wikipedia.org/wiki/Pastebin) espalhados pela internet é bem conhecido. Trata-se de uma forma muito simples de se compartilhar trechos de código ou arquivos-texto, principalmente em lugares onde seu tamanho é limitado, como em mensageiros instantâneos e no [Twitter](https://twitter.com/). Com diversas opções como a visibilidade da postagem, tempo de expiração e syntax highlighting, a forma mais comum de se postar em um pastebin é simplesmente abrir o site, mudar algumas destas opções se necessário, colar o texto e enviar. Mas e se você quiser algo mais simples ainda? Ou que facilite mais ainda o processo de postagem?

O excelente [Gist](https://gist.github.com/), do [Github](https://github.com/), além de possibilitar até mesmo o versionamento dos arquivos publicados, [possui uma API](http://develop.github.com/p/gist.html) para facilitar a integração com outros serviços, mas até o momento só é possível receber dados (e não postá-los). A ideia é que esta API está acessível a ferramentas simples em modo texto, como o [cURL](http://curl.haxx.se/), facilitando seu uso através do terminal, dispensando o processo de se acessar o site utilizando um navegador qualquer.

A partir deste conceito, surgiu o [PasteSH](https://github.com/myhro/PasteSH). Com uma interface extremamente simples, onde não é possível enviar um arquivo pela página web, mas apenas visualizá-lo. Ele é muito útil naqueles momentos em que você precisa copiar um arquivo de ou para um servidor e não quer perder tempo enviando via [SCP](http://en.wikipedia.org/wiki/Secure_copy) (principalmente se você está no Windows e precisa perder tempo abrindo o [WinSCP](http://winscp.net/)) ou copiando/colando via [PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/), o que nem sempre é cômodo ou possível. Uma versão pública está disponível no [p.myhro.net](http://p.myhro.net/) (não perca tempo o acessando diretamente via browser, isso não irá funcionar). Para postar um arquivo com o auxílio do cURL, basta utilizar os seguintes parâmetros:

    curl -F 'file=<receita_de_bolo_de_fuba.txt' http://p.myhro.net/

Onde "**file**" é o parâmetro necessário enviado via POST, bastando mudar apenas o nome do arquivo. O sinal "**<**" significa que o conteúdo do arquivo será enviado e não apenas seu nome. A partir daí o servidor lhe retornará o link único gerado, que é baseado no hash [SHA1](http://en.wikipedia.org/wiki/SHA-1) do conteúdo, com um pequeno workaround para se evitar hashs repetidos, dado que apenas os primeiros caracteres são utilizados. Caso ocorra algum erro no processo, isto também é informado.

Vamos realizar alguns testes para demonstrar seu funcionamento. O parâmetro "**-i**" do cURL (que está disponível no terminal do [Git for Windows](http://msysgit.github.com/)) foi utilizado para demonstrar os códigos de status do protocolo HTTP. Primeiro, a resposta em caso de acesso direto (a requisição é simplesmente redirecionada ao [Myhro.net](http://myhro.net/)):

    $ curl -i http://p.myhro.net/
    HTTP/1.1 301 Moved Permanently
    Server: nginx
    Date: Sun, 10 Jun 2012 16:17:36 GMT
    Content-Type: text/plain; charset="UTF-8"
    Transfer-Encoding: chunked
    Connection: keep-alive
    Location: http://myhro.net/
    
    Error: File too large or not present.

Requisição à uma postagem inexistente:

    $ curl -i http://p.myhro.net/xxxx
    HTTP/1.1 404 Not Found
    Server: nginx
    Date: Sun, 10 Jun 2012 16:18:25 GMT
    Content-Type: text/plain; charset="UTF-8"
    Transfer-Encoding: chunked
    Connection: keep-alive
    
    Error: 404 Not Found.

Postando um novo arquivo:

    $ curl -i -F 'file=<gpl-2.0.txt' http://p.myhro.net/
    HTTP/1.1 100 Continue
    
    HTTP/1.1 200 OK
    Server: nginx
    Date: Sun, 10 Jun 2012 16:20:16 GMT
    Content-Type: text/plain; charset="UTF-8"
    Transfer-Encoding: chunked
    Connection: keep-alive
    
    Link: http://p.myhro.net/eda9

Acessando um arquivo previamente enviado (o comando "head" foi utilizado para não imprimir o arquivo todo na tela):

    $ curl -i http://p.myhro.net/eda9 | head
    HTTP/1.1 200 OK
    Server: nginx
    Date: Sun, 10 Jun 2012 16:21:09 GMT
    Content-Type: text/plain; charset="UTF-8"
    Transfer-Encoding: chunked
    Connection: keep-alive
    
                        GNU GENERAL PUBLIC LICENSE
                           Version 2, June 1991




Como vocês podem ver, estou utilizando o [nginx](http://nginx.org/) para servir meus sites. [Há alguns meses atrás postei aqui falando sobre o fato](http://blog.myhro.info/2011/09/o-myhro-net-esta-de-volta/) de estar usando o [Lighttpd](http://www.lighttpd.net/), mas alguns bugs do mesmo simplesmente me desanimaram. Já havia me deparado com algumas imperfeições dele anteriormente (e não estou nem citando o fato dos _memory leaks_ que todo mundo alardeia porque não vivenciei isto), mas uma em especial impossibilitou a utilização do mesmo para servir o PasteSH. Qualquer requisição via cURL simplesmente retornava o erro 417 do HTTP. Lendo os bug reports, me deparei com [uma mensagem de mais de 5 anos atrás](http://redmine.lighttpd.net/issues/1017) dizendo que eles simplesmente não corrigiriam isto na linha 1.4.x, que é justamente a versão presente em todas as edições atuais (stable, testing e unstable) do [Debian](http://www.debian.org/).

Tive um certo trabalho para migrar todos os sites do Lighttpd pro nginx, mas me parece que tudo está funcionando corretamente. O repositório não-oficial [Dotdeb](http://www.dotdeb.org/) me ajudou muito, principalmente por disponibilizarem o [PHP-FPM](http://php-fpm.org/), que não está disponível no Debian Squeeze. Habilitei o [cache do Wordpress](http://wordpress.org/extend/plugins/wp-super-cache/) e o blog está praticamente voando. O nginx é um projeto realmente bacana, os russos mandaram muito bem dessa vez. Não é a toa que ele é o [terceiro (quase segundo) servidor web mais usado no mundo](http://news.netcraft.com/archives/2012/06/06/june-2012-web-server-survey.html) atualmente.

Disponibilizei instruções sobre a instalação do [PasteSH](https://github.com/myhro/PasteSH) no [README](https://github.com/myhro/PasteSH/blob/master/README.md) do repositório. Não há um instalador pois a ideia do mesmo é ser o mais simples possível, bastando apenas criar um banco de dados [SQLite](http://www.sqlite.org/) a partir do [schema disponilizado](https://github.com/myhro/PasteSH/blob/master/pastesh.sql) e alterar o caminho do mesmo no "[index.php](https://github.com/myhro/PasteSH/blob/master/index.php)". Recomendo apenas que o arquivo do banco de dados não fique na mesma pasta, dentro do diretório disponibilizado pelo servidor HTTP, para evitar que alguém que saiba o nome possa baixá-lo integralmente. É muito importante que a pasta onde o arquivo SQLite residir também tenha permissão de escrita, uma vez que em algumas operações [são criados alguns arquivos temporários](http://www.sqlite.org/tempfiles.html).
