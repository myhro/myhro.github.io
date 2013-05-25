---
date: 2013-02-23 10:57
layout: post
title: "Trocando o Iceweasel pelo Firefox"
categories: 
- debian
- firefox
---

Desde que troquei o Windows pelo [Debian](http://www.debian.org/) há pouco mais de seis meses, estive cada dia mais satisfeito com sua utilização. Por mais que o Windows 7 seja provavelmente a sua melhor versão, eu simplesmente não podia mais viver sem um terminal [Unix-like](https://en.wikipedia.org/wiki/Unix-like) de verdade. Não consigo me imaginar trabalhando sem ter utilitários como [find e grep](http://blog.sanctum.geek.nz/unix-as-ide-files/) a um comando de distância. Claro que eu poderia utilizar as versões dos mesmos portadas para Windows, seja com os utilitários que acompanham o [Git for Windows](http://msysgit.github.com/) ou mesmo o [Cygwin](http://www.cygwin.com/), mas estas não são nem de longe tão ágeis, me tomando alguns irritantes segundos cada vez que as executo.

Entretanto, por mais agradável que esteja sendo minha experiência com o Debian, uma coisa sempre me incomodou bastante, que é o fato do Debian não incluir uma versão atual do [Firefox](https://www.mozilla.org/en-US/firefox/new/) em seus repositórios. Na verdade o [Debian sequer distribui o Firefox](https://en.wikipedia.org/wiki/Mozilla_Corporation_software_rebranded_by_the_Debian_project) e sim uma versão alternativa, o [Iceweasel](http://www.geticeweasel.org/), devido ao fato do Firefox não ser completamente livre (o nome e a logo são marcas registradas), não podendo ser redistribuído com o mesmo nome caso tenha seu código modificado. Desta forma, não resta outra alternativa senão instalar uma versão mais recente do Firefox manualmente, caso você não queria continuar usando a versão [3.5](http://packages.debian.org/squeeze/iceweasel) [lançada em 2009](https://blog.mozilla.org/blog/2009/06/30/firefox-35-available-now/), que está disponível no repositório "stable" ou a [10.0](http://packages.debian.org/squeeze-backports/iceweasel), do início de 2012, disponível nos [backports](http://backports-master.debian.org/).

Vale ressaltar que se estas versões citadas do Firefox/Iceweasel satisfazem suas necessidades de navegação, não há motivo para instalar uma versão mais nova que as presentes nos repositórios. Os desenvolvedores do Debian se preocupam muito com estabilidade e segurança (principal motivo de não se encontrar as últimas versões dos pacotes na distribuição), portando correções presentes nas versões mais novas para estas mais antigas, quando necessário. Um dos motivos que me levaram a atualizar para a versão 19, por exemplo, é o [suporte a leitura de PDFs embutido no navegador](http://www.h-online.com/open/news/item/Firefox-19-brings-PDF-viewer-and-4-critical-security-fixes-1806437.html), sem plugins externos.

O primeiro passo é baixar o Firefox, seja a [última versão](http://www.mozilla.org/en-US/firefox/all/) ou a [versão de suporte extendido](http://www.mozilla.org/en-US/firefox/organizations/all.html). Se você utiliza a versão 64 bits do Debian, mais conhecida como [amd64](http://www.debian.org/ports/amd64/), é necessário efetuar o download direto do [FTP da Mozilla](https://ftp.mozilla.org/pub/mozilla.org/firefox/releases/), escolhendo a versão "linux-x86\_64". É possível começar a utilizar o Firefox de imediato, apenas descompactando o arquivo baixado, mas é muito mais interessante integrá-lo corretamente ao sistema. Ao invés de descompactá-lo em sua pasta pessoal, é recomendável deixá-lo em uma pasta como a `/usr/local/` ou `/opt/`, de forma que outros usuários do sistema também possam utilizá-lo, se for o caso. Além disto, manter a instalação em uma pasta onde seu usuário não tenha permissão de escrita é uma nova camada de segurança, já que os arquivos lá seriam modificados apenas se você utilizasse o "root" explicitamente para isto. Descompactando o arquivo [bz2](https://en.wikipedia.org/wiki/Bzip2):

    [myhro@squeeze:~]$ sudo tar jxf firefox-19.0.tar.bz2 -C /opt/

O próximo passo é criar um arquivo chamado `firefox.desktop` na pasta `/usr/share/applications/` com o seguinte conteúdo:

```
[Desktop Entry]
Encoding=UTF-8
Name=Mozilla Firefox
Comment=Browse the World Wide Web
Type=Application
Terminal=false
Exec=/opt/firefox/firefox %U
Icon=/opt/firefox/browser/icons/mozicon128.png
StartupNotify=true
Categories=Network;WebBrowser;
```

Desta forma, um atalho do Firefox será criado nos menus do Gnome/LXDE (e provavelmente outros gerenciadores de janelas, sendo estes os que tenho contato). Com apenas estes passos você já terá um atalho funcional e poderá utilizá-lo normalmente, mas o fato dele não estar definido como navegador padrão fará com que qualquer link clicado em outro programa não seja aberto por ele. Para solucionar isto, utilizaremos o comando "[update-alternatives](http://www.debian-administration.org/articles/91)", adicionando o Firefox e escolhendo-o como navegador padrão do ambiente gráfico.

    [myhro@squeeze:~]$ sudo update-alternatives --install /usr/bin/x-www-browser x-www-browser /opt/firefox/firefox 50
    [myhro@squeeze:~]$ sudo update-alternatives --config x-www-browser 
    There are 3 choices for the alternative x-www-browser (providing /usr/bin/x-www-browser).

      Selection    Path                        Priority   Status
    ------------------------------------------------------------
    * 0            /usr/bin/google-chrome       200       auto mode
      1            /usr/bin/google-chrome       200       manual mode
      2            /usr/bin/iceweasel           70        manual mode
      3            /opt/firefox/firefox   50        manual mode

    Press enter to keep the current choice[*], or type selection number: 3
    update-alternatives: using /opt/firefox/firefox to provide /usr/bin/x-www-browser (x-www-browser) in manual mode.

No caso do Gnome, é necessário definir o Firefox como alternativa para o `gnome-www-browser` também. O comando é praticamente o mesmo, apenas substituindo `x-www-browser` por `gnome-www-browser`:

    [myhro@squeeze:~]$ sudo update-alternatives --install /usr/bin/gnome-www-browser gnome-www-browser /opt/firefox/firefox 50
    [myhro@squeeze:~]$ sudo update-alternatives --config gnome-www-browser
    There are 3 choices for the alternative gnome-www-browser (providing /usr/bin/gnome-www-browser).

      Selection    Path                        Priority   Status
    ------------------------------------------------------------
    * 0            /usr/bin/google-chrome       200       auto mode
      1            /usr/bin/google-chrome       200       manual mode
      2            /usr/bin/iceweasel           70        manual mode
      3            /opt/firefox/firefox   50        manual mode

    Press enter to keep the current choice[*], or type selection number: 3
    update-alternatives: using /opt/firefox/firefox to provide /usr/bin/gnome-www-browser (gnome-www-browser) in manual mode.

Para testar se o Firefox está realmente configurado como navegador padrão, você pode clicar em um link qualquer ou testá-lo diretamente com o comando "[xdg-open](http://portland.freedesktop.org/xdg-utils-1.0/xdg-open.html)":

    [myhro@squeeze:~]$ xdg-open http://www.google.com/

Se a URL for aberta corretamente no Firefox, está tudo configurado corretamente. Caso contrário, veja se você o definiu corretamente como navegador padrão, repetindo os passos anteriores. Você pode ser tentado a desinstalar de vez o pacote do Iceweasel, mas esta é uma ação qual não recomendo, por basicamente dois motivos: eles podem ter dependências em comum (o Firefox não mais abriria quando você removesse o Iceweasel e suas dependências) e no caso do Gnome, a desinstalação do Iceweasel o obriga a instalar o [Epiphany](http://projects.gnome.org/epiphany/), o que lhe deixaria com um navegador a mais no sistema do mesmo jeito.

Referências:  
1. [How to install real Firefox on Debian?](http://superuser.com/questions/322376/how-to-install-real-firefox-on-debian)  
2. [The problem with Iceweasel and why I am now using Firefox](http://techpatterns.com/forums/about1435.html)
