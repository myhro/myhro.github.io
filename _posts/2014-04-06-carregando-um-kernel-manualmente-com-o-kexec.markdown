---
date: 2014-04-06 14:55
layout: post
title: "Carregando um kernel manualmente com o kexec"
...

Conheci o [kexec](https://wiki.archlinux.org/index.php/kexec) por um acaso. É o tipo de ferramenta que, se alguém tivesse me mostrado sem exemplificar com um caso de uso específico, não enxergaria a menor utilidade. A ideia é muito simples, como seu próprio nome sugere: executar (carregar) um kernel. Você pode se perguntar porque alguém precisaria de uma ferramenta para realizar um processo que normalmente é executado por um gerenciador de boot, como o [GRUB](https://www.gnu.org/software/grub/). Uma das respostas possíveis é: nem sempre você tem a possibilidade de alterar as configurações de inicialização da máquina.

Este é o caso da [DigitalOcean](https://www.digitalocean.com/?refcode=f3170b9eaf20), uma empresa nova-iorquina que oferece servidores virtuais a preços extremamente acessíveis. O painel de administração das máquinas virtuais é muito bem feito, possuindo uma interface extremamente simples, que ao mesmo tempo oferece todas as opções necessárias para configurá-las. Entretanto, há um problema: só é possível inicializar as máquinas Linux (únicas oferecidas até o momento) com os kerneis pré-definidos por eles. Não é possível, por exemplo, recompilar seu próprio kernel ou mesmo trocá-lo por uma versão atualizada com novas correções de segurança.

Isto normalmente não é um problema, pois eles oferecem uma quantidade até razoável de kerneis diferentes no painel de administração. O problema é quando você precisa de uma combinação menos convencional, como atualizar o Debian para a versão `testing` ou `unstable`, utilizando um kernel qual não está disponível. E isto era justamente o que eu precisava para rodar o [Docker](https://www.docker.io/) no Debian, ou pelo menos [atualizar o kernel](http://docs.docker.io/en/latest/installation/ubuntulinux/#ubuntu-precise-12-04-lts-64-bit), se não fosse fazer isto com todo o sistema.

A solução para esta situação é carregar um kernel "na mão", utilizando o comando `kexec`, que no Debian está disponível no pacote [kexec-tools](https://packages.debian.org/wheezy/kexec-tools). Partindo do pressuposto que você já tenha instalado (via `apt-get`, compilado, etc.) o kernel qual deseja carregar, basta executar o comando `kexec -l [arquivo-kernel] --initrd=[arquivo-initrd]`. Por exemplo:

    [root@debian-docker:~]# uname -a
    Linux debian-docker 3.2.0-4-amd64 #1 SMP Debian 3.2.41-2+deb7u2 x86_64 GNU/Linux
    [root@debian-docker:~]# ls -lh /boot/
    total 33M
    -rw-r--r-- 1 root root 148K Mar 26 05:11 config-3.13-1-amd64
    -rw-r--r-- 1 root root 127K Mar 26  2013 config-3.2.0-4-amd64
    drwxr-xr-x 2 root root 4.0K Apr  6 19:42 grub
    -rw-r--r-- 1 root root  13M Apr  6 19:50 initrd.img-3.13-1-amd64
    -rw-r--r-- 1 root root 9.8M May  3  2013 initrd.img-3.2.0-4-amd64
    -rw-r--r-- 1 root root 2.3M Mar 26 05:11 System.map-3.13-1-amd64
    -rw-r--r-- 1 root root 2.1M Mar 26  2013 System.map-3.2.0-4-amd64
    -rw-r--r-- 1 root root 2.8M Mar 26 05:08 vmlinuz-3.13-1-amd64
    -rw-r--r-- 1 root root 2.8M Mar 26  2013 vmlinuz-3.2.0-4-amd64
    [root@debian-docker:~]# kexec -l /boot/vmlinuz-3.13-1-amd64 --initrd=/boot/initrd.img-3.13-1-amd64

Neste momento, o normal seria executar o kernel previamente carregado com o comando `kexec -e`. Entretanto, não obtive sucesso quando tentei fazer isso e em todas as vezes a máquina simplesmente travava. O que resolveu meu problema foi executar o comando `dpkg-reconfigure kexec-tools`, respondendo `Yes` na pergunta "_Should kexec-tools handle reboots?_" e `No` na pergunta "_Read GRUB configuration file to load the kernel?_". Isto faz com que o kernel a ser inicializado passe a ser o que foi carregado no kexec, sem influência do que estiver configurado no GRUB.

A partir disto, basta reiniciar a máquina, seja pelo painel de administração ou através do comando `reboot`. Na próxima vez que você se conectar, verifique a versão do kernel, para confirmar se tudo ocorreu como esperado:

    [root@debian-docker:~]# uname -a
    Linux debian-docker 3.13-1-amd64 #1 SMP Debian 3.13.7-1 (2014-03-25) x86_64 GNU/Linux
