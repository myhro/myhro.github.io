---
date: 2013-11-01 14:19
layout: post
title: "Vagrant: Introdução"
categories: 
- devops
- vagrant
---

O Vagrant é uma ferramenta criada originalmente para o VirtualBox, mas que hoje suporta uma grande variedade de sistemas de virtualização, qual fornece uma fina camada para facilitar a criação, gerenciamento e utilização de ambientes de desenvolvimento virtuais. O velho problema conhecido como "na minha máquina funciona" é sanado pela raiz, visto que através de pouquíssimos passos, todos os desenvolvedores tem acesso a um ambiente praticamente idêntico, onde as únicas diferenças residem na versão do VirtualBox instalada ou nas modificações realizadas no sistema operacional da máquina virtual utilizada.

Para começar a utilizar o Vagrant, são necessários basicamente três componentes:

* O [VirtualBox](https://www.virtualbox.org/), que além de ser o [_provider_](http://docs.vagrantup.com/v2/providers/index.html) padrão, é uma excelente ferramenta de virtualização, gratuita e largamente utilizada.
* O próprio [Vagrant](http://www.vagrantup.com/), que é responsável por fornecer os comandos e ler os arquivos de configuração para gerenciamento das máquinas virtuais.
* Uma _box_, que nada mais é do que uma máquina virtual criada no VirtualBox ou outro _provider_ cujas configurações e imagem de disco foram exportadas e compactadas em um único arquivo.

O processo de instalação de ambos VirtualBox e Vagrant varia de acordo com o sistema operacional utilizado, podendo ser Windows, Linux ou Mac OS X, mas ainda assim é muito simples. Vale ressaltar que o VirtualBox também suporta Solaris, mas o Vagrant não. O que importa é que ao final você terá o comando `vagrant`, através do qual realizará todo o processo de adicionar/criar/inicializar/desligar máquinas virtuais, sem precisar utilizar a interface do VirtualBox diretamente para isto.

Após realizar a instalação de ambos, é necessário adicionar pelo menos uma nova _box_. Há uma página com [diversas boxes de várias distribuições Linux ou BSD](http://www.vagrantbox.es/), mas neste exemplo utilizaremos uma que criei a partir de uma instalação mínima do Debian "Wheezy" 7.0. A sintaxe do comando é `vagrant box add {nome} {url}`, onde o nome escolhido será utilizado para se referir à mesma em outros comandos. A URL não precissa ser necessariamente um link HTTP, podendo ser também o caminho de um arquivo em outro diretório, por exemplo.

    [myhro@wheezy:~]$ vagrant box list
    There are no installed boxes! Use `vagrant box add` to add some.
    [myhro@wheezy:~]$ vagrant box add wheezy32 http://myhro.info/dl/wheezy32.box 
    Downloading or copying the box...
    Extracting box...te: 2.6M/s, Estimated time remaining: 0:00:01)
    Successfully added box 'wheezy32' with provider 'virtualbox'!
    [myhro@wheezy:~]$ vagrant box list
    wheezy32 (virtualbox)

Após a _box_ ser copiada, basta, na pasta do projeto qual irá utilizar esta máquina virtual, utilizar o comando `vagrant init {nome}`. É importante que se faça isso na pasta correta por dois motivos: o primeiro é que a pasta atual será compartilhada no diretório `/vagrant/` da máquina virtual, não necessitando copiar seus arquivos por outros meios para acessá-los. O segundo motivo é que será criado um arquivo chamado `VagrantFile`, que é um simples arquivo-texto onde todas as configurações necessárias para adaptação da máquina estarão presentes. Desta forma, este arquivo pode ser compartilhado com os demais membros do projeto, tornando o ambiente ainda mais homogêneo.

    [myhro@wheezy:~]$ cd project/
    [myhro@wheezy:~/project]$ vagrant init wheezy32
    A `Vagrantfile` has been placed in this directory. You are now
    ready to `vagrant up` your first virtual environment! Please read
    the comments in the Vagrantfile as well as documentation on
    `vagrantup.com` for more information on using Vagrant.

Em seguida, basta colocar a máquina pra rodar com o comando `vagrant up`:

    [myhro@wheezy:~/project]$ vagrant up
    Bringing machine 'default' up with 'virtualbox' provider...
    [default] Importing base box 'wheezy32'...
    [default] Matching MAC address for NAT networking...
    [default] Setting the name of the VM...
    [default] Clearing any previously set forwarded ports...
    [default] Creating shared folders metadata...
    [default] Clearing any previously set network interfaces...
    [default] Preparing network interfaces based on configuration...
    [default] Forwarding ports...
    [default] -- 22 => 2222 (adapter 1)
    [default] Booting VM...
    [default] Waiting for machine to boot. This may take a few minutes...
    [default] Machine booted and ready!
    [default] Mounting shared folders...
    [default] -- /vagrant

A partir daí, é possível, por exemplo, logar na máquina com o comando `vagrant ssh` e instalar/configurar/executar o que for necessário. Os exemplos contidos no arquivo `VagrantFile` ensinam como redirecionar outras portas, fazendo com que um servidor web rodando na máquina virtual se torne acessível a partir do browser na máquina físical qual a está rodando.

    myhro@wheezy:~/project]$ vagrant ssh
    Linux wheezy32 3.2.0-4-686-pae #1 SMP Debian 3.2.51-1 i686

    The programs included with the Debian GNU/Linux system are free software;
    the exact distribution terms for each program are described in the
    individual files in /usr/share/doc/*/copyright.

    Debian GNU/Linux comes with ABSOLUTELY NO WARRANTY, to the extent
    permitted by applicable law.
    Last login: Wed Jun 19 15:19:38 2013
    [vagrant@wheezy32:~]$ df -h
    Filesystem      Size  Used Avail Use% Mounted on
    rootfs          4.0G 1022M  2.8G  27% /
    udev             10M     0   10M   0% /dev
    tmpfs            51M  196K   51M   1% /run
    /dev/sda1       4.0G 1022M  2.8G  27% /
    tmpfs           5.0M     0  5.0M   0% /run/lock
    tmpfs           101M     0  101M   0% /run/shm
    none            257G  120G  138G  47% /vagrant
    [vagrant@wheezy32:~]$ free -m
    total       used       free     shared    buffers     cached
    Mem:           502        103        398          0         11         62
    -/+ buffers/cache:         29        472
    Swap:            0          0          0
    [vagrant@wheezy32:~]$ logout
    Connection to 127.0.0.1 closed.
    [myhro@wheezy:~/project]$ 

E após utilizar a máquina, basta colocá-la para "dormir" com o comando `vagrant suspend` ou desligá-la com `vagrant halt`. Mas o comando mais legal mesmo é o salva-vidas `vagrant destroy`:

    [myhro@wheezy:~/project]$ vagrant destroy
    Are you sure you want to destroy the 'default' VM? [y/N] y
    [default] Forcing shutdown of VM...
    [default] Destroying VM and associated drives...

Este comando, como o próprio nome sugere, destrói completamente a máquina virtual. A princípio pode parecer algo exagerado, mas é a coisa mais rápida e prática a se fazer quando algo der errado, como quando a máquina não iniciar mais depois de alguma modificação realizada. Depois disso, basta iniciá-la novamente, recriando-a do zero, com comando `vagrant up`, que também serve para quando a mesma está desligada ou suspensa.
