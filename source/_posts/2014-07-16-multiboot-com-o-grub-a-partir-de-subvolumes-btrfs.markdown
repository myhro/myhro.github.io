---
date: 2014-07-16 10:53
layout: post
title: "Multiboot com o GRUB a partir de subvolumes Btrfs"
categories:
- btrfs
- grub
---

Estava eu lendo o excelente (e gratuito) livro do [Raphaël Hertzog][raphael] e [Roland Mas][roland] [The Debian Administrator's Handbook: Debian Wheezy from Discovery to Mastery][debian-handbook], quando me deparei com o capítulo sobre [Logical Volume Manager (LVM)][lvm-cap]. LVM é um daqueles assuntos que sempre ouvia falar, mas deixava de fuçar por pura preguiça e comodismo, no estilo "_gosto dos meus discos particionados do jeito que estão_", sem compreender de fato os benefícios quais poderiam ser obtidos com um mínimo de esforço.

A ideia de ter uma ou mais partições físicas no disco (_Physical Volume_ - PV) dividadas em volumes lógicos (_Logical Volumes_ - LV) me resolveria um problema qual venho tendo desde quando [passei utilizar o disco encriptado com LUKS/cm-crypt][wheezy-luks]: a impossibilidade de aumentar ou diminuir o tamanho das partições. Se eu estivesse utilizando LVM desde o começo, isto nunca teria me acontecido. Esta abordagem, entretanto, ainda possui uma limitação: embora volumes lógicos possam ser redimensionados, às vezes sem nem mesmo precisa desmontar o sistema de arquivos, estes ainda precisam ter um tamanho fixo definido.

Seria possível conviver com esta limitação não-crucial perfeitamente, desde que uma solução mais flexível não existisse: os [subvolumes do Btrfs][subvolumes]. É claro que eles não são um substituto perfeito, pois não podem ser tratados como um dispositivo de bloco e particionados em um sistema de arquivos diferentes, como no caso dos volumes lógicos do LVM. Entretanto, como minha necessidade era a de apenas isolar volumes _como se fossem_ sistemas de arquivos separados (mas ainda do mesmo tipo), a solução se encaixaria perfeitamente. E no caso do [Btrfs][btrfs], a utilização de [quotas][quota] para limitar a utilização do subvolume é opcional.

A utilização de subvolumes no Btrfs é tão simples quanto criar um diretório. Um processo um pouco mais complicado, mas que ainda não é muito difícil de ser feito, é dar boot em uma instalação do Linux localizada em um subvolume. O complicador, neste caso, é instruir ao [GRUB][grub] como encontrar cada diretório `/boot` e impedir que uma instalação sobrescreva a configuração do GRUB da outra - ou pelo menos tentar, porque às vezes o comando `grub-install` é chamado inevitavelmente, como quando se atualiza entre diferentes versões do Debian (do `stable` pro `testing`, por exemplo).

Como em tantas outros casos na computação, há diversas formas de se realizar este processo. O que será descrito aqui é apenas o mais simples que encontrei até o momento. Durante minha pesquisa, vi [instruirem a mudar o subvolume padrão][change-subvolume] ou [editar manualmente][grub-cfg] o `grub.cfg`, coisas que você não precisa ou não deve fazer de forma alguma. Vale ressaltar também que o [Btrfs é considerado experimental][btrfs-experimental] até o presente momento e que você não deve realizar nenhum dos passos descritos aqui sem o [devido backup dos seus dados][codinghorror-backup], a não ser que não se importe em perdê-los. Ao mesmo tempo, há pessoas [o utilizando há anos sem nenhum incidente sério e se deliciando com as possibilidades trazidas][ars-btrfs].

Todo o processo a seguir foi realizado a partir de uma instalação padrão do Debian Wheezy (mais especificamente a [versão 7.6][debian-76]) em uma única partição formatada como Btrfs.

## Movendo a instalação padrão para um subvolume

Diferentemente do Ubuntu e seus derivados, que criam dois subvolumes (`@` e `@home`) na instalação padrão em um sistema Btrfs, o Debian simplesmente instala o sistema na raíz da partição, sem criar nenhum subvolume. É preciso, então, criar um subvolume manualmente e mover a instalação para este. Como moveremos o próprio sistema, não é possível fazer isto iniciando a partir do mesmo. É preciso utilizar um outro sistema, a partir de um LiveCD ou pendrive USB preparado para isto - basta que este tenha suporte ao Btrfs.

Montando a raíz do sistema e verificando o resultado:

    root@xubuntu:~# mount /dev/sda1 /mnt/
    root@xubuntu:~# mount | grep mnt
    /dev/sda1 on /mnt type btrfs (rw)

Verificando a não existência de subvolumes e criando dois novos:

    root@xubuntu:~# btrfs subvol list /mnt/
    root@xubuntu:~# btrfs subvol create /mnt/@stable
    Create subvolume '/mnt/@stable'
    root@xubuntu:~# btrfs subvol create /mnt/@home
    Create subvolume '/mnt/@home'
    root@xubuntu:~# btrfs subvol list /mnt/
    ID 260 gen 3727 top level 5 path @stable
    ID 261 gen 3728 top level 5 path @home

Movendo a instalação para o subvolume `@stable`:

    root@xubuntu:~# cd /mnt/
    root@xubuntu:/mnt# for i in $(ls -1 | egrep -v '(@|home)'); do mv $i @stable/; done

Movendo a `/home` para o seu subvolume. O diretório criado dentro do subvolume `@stable` é para que o subvolume `@home` possa ser montado lá:

    root@xubuntu:/mnt# mv home/* @home/
    root@xubuntu:/mnt# rmdir home/
    root@xubuntu:/mnt# mkdir @stable/home

Verificando o resultado, é possível notar que na utilização, os subvolumes não diferem muito de simples diretórios:

    root@xubuntu:/mnt# ls -lh
    total 0
    drwxr-xr-x 1 root root  14 Jul 16 15:49 @home
    drwxr-xr-x 1 root root 180 Jul 16 15:49 @stable
    root@xubuntu:/mnt# ls -lh @home/
    total 0
    drwxr-xr-x 1 1000 1000 54 Jul 16 14:22 usuario
    root@xubuntu:/mnt# ls -lh @stable/
    total 8.0K
    drwxr-xr-x 1 root root 1.3K Jul 16 14:04 bin
    drwxr-xr-x 1 root root  186 Jul 16 14:05 boot
    drwxr-xr-x 1 root root  418 Jul 16 13:52 dev
    drwxr-xr-x 1 root root 2.5K Jul 16 15:32 etc
    drwxr-xr-x 1 root root    0 Jul 16 15:49 home
    lrwxrwxrwx 1 root root   30 Jul 16 13:52 initrd.img -> /boot/initrd.img-3.2.0-4-amd64
    drwxr-xr-x 1 root root  532 Jul 16 13:54 lib
    drwxr-xr-x 1 root root   40 Jul 16 13:52 lib64
    drwxr-xr-x 1 root root   22 Jul 16 13:51 media
    drwxr-xr-x 1 root root    0 Jun 11 21:07 mnt
    drwxr-xr-x 1 root root    0 Jul 16 13:51 opt
    drwxr-xr-x 1 root root    0 Jun 11 21:07 proc
    drwx------ 1 root root   74 Jul 16 15:25 root
    drwxr-xr-x 1 root root    0 Jul 16 15:24 run
    drwxr-xr-x 1 root root 2.4K Jul 16 15:24 sbin
    drwxr-xr-x 1 root root    0 Jun 10  2012 selinux
    drwxr-xr-x 1 root root    0 Jul 16 13:51 srv
    drwxr-xr-x 1 root root    0 Jul 14  2013 sys
    drwxrwxrwt 1 root root    0 Jul 16 15:24 tmp
    drwxr-xr-x 1 root root   70 Jul 16 13:51 usr
    drwxr-xr-x 1 root root   90 Jul 16 13:51 var
    lrwxrwxrwx 1 root root   26 Jul 16 13:52 vmlinuz -> boot/vmlinuz-3.2.0-4-amd64

A diferença está em como eles se comportam e o que podemos fazer com eles. O que faremos agora é montar o subvolume `@stable` como se fosse uma partição, montando nele os diretórios `/dev`, `/proc` e `/sys` para que possamos rodar os comandos do GRUB quando fizermos `chroot` nele:

    root@xubuntu:/mnt# cd
    root@xubuntu:~# umount /mnt
    root@xubuntu:~# mount -o subvol=@stable /dev/sda1 /mnt/
    root@xubuntu:~# for i in /dev /proc /sys; do mount -o bind $i /mnt$i; done
    root@xubuntu:~# chroot /mnt/
    root@xubuntu:/# update-grub
    Generating grub.cfg ...
    Found linux image: /boot/vmlinuz-3.2.0-4-amd64
    Found initrd image: /boot/initrd.img-3.2.0-4-amd64
    done

Verificando o resultado da atualização do GRUB, o importante aqui são dois detalhes envolvendo o subvolume `@stable`: este deve aparecer como se fosse o diretório pai do `/boot` e também estar presente no parâmetro `rootflags=subvol=@stable`. Do contrário, o GRUB não encontraria o kernel a ser inicializado e o subvolume não seria corretamente montado como diretório raíz:

    root@xubuntu:/# grep '@' /boot/grub/grub.cfg
    if loadfont /@stable/usr/share/grub/unicode.pf2 ; then
      set locale_dir=($root)/@stable/boot/grub/locale
        linux   /@stable/boot/vmlinuz-3.2.0-4-amd64 root=UUID=6ce02f0b-4fe0-447e-a418-a1c17f029233 ro rootflags=subvol=@stable  quiet
        initrd  /@stable/boot/initrd.img-3.2.0-4-amd64
        linux   /@stable/boot/vmlinuz-3.2.0-4-amd64 root=UUID=6ce02f0b-4fe0-447e-a418-a1c17f029233 ro single rootflags=subvol=@stable
        initrd  /@stable/boot/initrd.img-3.2.0-4-amd64

Estando tudo como esperado, é necessário executar a instalação do GRUB novamente:

    root@xubuntu:/# grub-install /dev/sda
    Installation finished. No error reported.

É preciso editar também o `/etc/fstab`, informando o ponto de montagem dos subvolumes. O resultado deve ser parecido com este:

```
# /etc/fstab: static file system information.
#
(...)
#
# <file system> <mount point>   <type>  <options>       <dump>  <pass>
# / was on /dev/sda1 during installation
UUID=6ce02f0b-4fe0-447e-a418-a1c17f029233    /        btrfs    defaults,subvol=@stable    0    0
UUID=6ce02f0b-4fe0-447e-a418-a1c17f029233    /home    btrfs    defaults,subvol=@home      0    0
(...)
```

Em seguida, basta sair do chroot e reiniciar o sistema normalmente:

    root@xubuntu:/# exit
    exit
    root@xubuntu:~# reboot
    root@xubuntu:~#
    Broadcast message from xubuntu@xubuntu
        (/dev/pts/8) at 16:18 ...

    The system is going down for reboot NOW!

Ao logar no sistema, podemos verificar que os subvolumes foram montados corretamente:

    root@stable:~# df -h
    Filesystem                                              Size  Used Avail Use% Mounted on
    rootfs                                                  8.0G  541M  6.8G   8% /
    udev                                                     10M     0   10M   0% /dev
    tmpfs                                                    50M  216K   50M   1% /run
    /dev/disk/by-uuid/6ce02f0b-4fe0-447e-a418-a1c17f029233  8.0G  541M  6.8G   8% /
    tmpfs                                                   5.0M     0  5.0M   0% /run/lock
    tmpfs                                                   100M     0  100M   0% /run/shm
    /dev/sda1                                               8.0G  541M  6.8G   8% /home
    root@stable:~# ls -lh /home/
    total 0
    drwxr-xr-x 1 usuario usuario 54 Jul 16 11:22 usuario

## Clonando o sistema em outro subvolume

Até o presente momento, apenas movemos a instalação do Debian para um subvolume, criando outro para a `/home` e configuramos o sistema para dar boot na nova configuração. Isto não é muito diferente do que a instalação do Ubuntu faria por padrão. Mas é agora que as coisas começam a ficar interessantes: vamos clonar a instalação atual, realizando um snapshot do subvolume que possa ser atualizado para o Debian Jessie - sem influenciar em nada na instalação atual.

Como estamos utilizando um subvolume como raíz, é necessário montar a partição sem especificar um dos subvolumes criados, para que possamos criar outros:

    root@stable:~# mount /dev/sda1 /mnt/
    root@stable:~# ls -lh /mnt/
    total 0
    drwxr-xr-x 1 root root  14 Jul 16 12:49 @home
    drwxr-xr-x 1 root root 180 Jul 16 12:49 @stable
    root@stable:~# btrfs subvol list /mnt/
    ID 260 top level 5 path @stable
    ID 261 top level 5 path @home
    root@stable:~# btrfs subvol snapshot /mnt/@stable /mnt/@testing
    Create a snapshot of '/mnt/@stable' in '/mnt/@testing'
    root@stable:~# btrfs subvol list /mnt/
    ID 260 top level 5 path @stable
    ID 261 top level 5 path @home
    ID 262 top level 5 path @testing

É importante frisar: este processo de criação do snapshot é instantâneo e praticamente não consome espaço em disco. Devido a natureza [_Copy-on-write_ (COW)][lwn-btrfs] do Btrfs, este snapshot é apenas uma referência ao conteúdo atual do subvolume qual o snapshot foi realizado - os dados serão gravados a medida que o subvolume do snapshot for modificado.

Realizaremos novamente o processo de chroot, desta vez no clone do subvolume, realizando as mesmas alterações no `/etc/fstab` e rodando o `update-grub`, com cuidado para não executar o `grub-install` pois não é isto o que queremos, pois do contrário não conseguiríamos mais inicializar no sistema atual.

    root@stable:~# umount /mnt
    root@stable:~# mount -o subvol=@testing /dev/sda1 /mnt/
    root@stable:~# for i in /dev /proc /sys; do mount -o bind $i /mnt$i; done
    root@stable:~# chroot /mnt/
    root@stable:/# sed -i s/@stable/@testing/ /etc/fstab
    root@stable:/# update-grub
    Generating grub.cfg ...
    Found linux image: /boot/vmlinuz-3.2.0-4-amd64
    Found initrd image: /boot/initrd.img-3.2.0-4-amd64
    done

Vamos aproveitar também para alterar o hostname, de forma a identificar mais facilmente se inicializamos o sistema correto logo mais:

    root@stable:/# sed -i s/stable/testing/ /etc/hostname
    root@stable:/# sed -i s/stable/testing/ /etc/hosts

Saindo do chroot, é necessário alterar as configurações do GRUB do primeiro sistema, pois o que faremos é um [_chainload_ entre as instalações do GRUB][grub-chainload]. Este recurso normalmente é utilizado para que o GRUB passe o processo de instalação para o outro sistema operacional não suportado, como o Windows, mas aqui faremos com que exista uma opção no menu de inicialização que simplesmente carregue a configuração do GRUB de outro subvolume.

    root@stable:/# exit
    root@stable:~# vi /etc/grub.d/40_custom

Adicionaremos uma entrada muito simples, consistindo em apenas quatro linhas. Seu arquivo `40_custom` deve ficar parecido com isto:

```
#!/bin/sh
exec tail -n +3 $0
# This file provides an easy way to add custom menu entries.  Simply type the
# menu entries you want to add after this comment.  Be careful not to change
# the 'exec tail' line above.
menuentry 'Debian testing' {
    set root='(hd0,msdos1)'
    configfile /@testing/boot/grub/grub.cfg
}
```

A linha `set root=...` pode variar conforme sua configuração, de acordo com a [nomenclatura utilizada pelo GRUB][grub-naming]. Neste caso, `hd0` é o primeiro disco (`sda`) e `msdos1` é a primeira partição (`sda1`). Em seguida, basta rodar o `update-grub` e reiniciar a máquina para ter acesso a opção de inicialização no novo subvolume. É normal que neste ponto não apareça nada sobre a opção que foi adicionada ao `40_custom`, pois o `update-grub` exibe apenas os kerneis quais detectou automaticamente:

    root@stable:~# update-grub
    Generating grub.cfg ...
    Found linux image: /boot/vmlinuz-3.2.0-4-amd64
    Found initrd image: /boot/initrd.img-3.2.0-4-amd64
    done
    root@stable:~# reboot

    Broadcast message from root@stable (pts/0) (Wed Jul 16 14:08:21 2014):

    The system is going down for reboot NOW!

Podemos ver a opção do chainload "Debian testing":

{% img center /images/2014/grub.png %}

E escolhendo-a, somos apresentados a tela do GRUB do volume `@testing`, qual só possui seus próprios kerneis, acompanhada da opção de voltar pra tela anterior com a tecla `ESC`:

{% img center /images/2014/grub-chainload.png %}

Após a inicialização, podemos conferir a montagem correta dos subvolumes, com o `@home` compartilhado entre ambas as instalações `stable` e `testing`:

    root@testing:~# df -h
    Filesystem                                              Size  Used Avail Use% Mounted on
    rootfs                                                  8.0G  542M  6.8G   8% /
    udev                                                     10M     0   10M   0% /dev
    tmpfs                                                    50M  216K   50M   1% /run
    /dev/disk/by-uuid/6ce02f0b-4fe0-447e-a418-a1c17f029233  8.0G  542M  6.8G   8% /
    tmpfs                                                   5.0M     0  5.0M   0% /run/lock
    tmpfs                                                   100M     0  100M   0% /run/shm
    /dev/sda1                                               8.0G  542M  6.8G   8% /home
    root@testing:~# ls -lh /home/
    total 0
    drwxr-xr-x 1 usuario usuario 54 Jul 16 11:22 usuario

A partir deste momento, você pode utilizar o sistema normalmente. O fato dele estar em um subvolume, tendo sido inicializado a partir de um encadeamento de configurações do GRUB não influenciará em praticamente nada no seu fucionamento, com exceção de que você deve tomar cuidado para que o comando `grub-install` não sobrescreva a configuração atual. Se isto acontecer, será possível inicializar apenas este subvolume `@testing`, sendo necessário realizar novamente o processo de restauração do GRUB, montando o subvolume `@stable`, pastas especiais e executando o `grub-install` dentro do chroot.

Você poderia, também, utilizar uma configuração do `40_custom` nesta instalação que possibilitasse o encadamento de outro GRUB, mas o processo pode ficar confuso, já que a ordem do menu seria alterada dependendo de qual instalação sobrescreveu o GRUB por último. Mesmo assim, esta pode ser uma opção melhor do que simplesmente perder o acesso às outras instalações devido ao acontecimento de algum imprevisto.

Referências:

1. [Make maintaining GRUB easier for multi-booters, a "How To."][grub-multiboot]
2. [More BTRFS fun: Multibooting to subvolumes on the same partition.][btrfs-multiboot]


[ars-btrfs]: http://arstechnica.com/information-technology/2014/01/bitrot-and-atomic-cows-inside-next-gen-filesystems/
[btrfs-experimental]: https://btrfs.wiki.kernel.org/index.php/FAQ#Is_btrfs_stable.3F
[btrfs-multiboot]: https://www.kubuntuforums.net/showthread.php?60321-More-BTRFS-fun-Multibooting-to-subvolumes-on-the-same-partition&s=09660f70358816958ff68192a50fe40e
[btrfs]: https://btrfs.wiki.kernel.org/
[change-subvolume]: http://blog.kourim.net/installing-debian-on-btrfs-subvolume
[codinghorror-backup]: http://blog.codinghorror.com/international-backup-awareness-day/
[debian-76]: https://www.debian.org/News/2014/20140712
[debian-handbook]: http://debian-handbook.info/
[debian]: https://www.debian.org/
[grub-cfg]: http://askubuntu.com/a/400666
[grub-chainload]: https://www.gnu.org/software/grub/manual/legacy/Chain_002dloading.html
[grub-multiboot]: https://www.kubuntuforums.net/showthread.php?60180-Make-maintaining-GRUB-easier-for-multi-booters-a-quot-How-To-quot
[grub-naming]: https://www.gnu.org/software/grub/manual/grub.html#Naming-convention
[grub]: https://www.gnu.org/software/grub/
[lvm-cap]: http://debian-handbook.info/browse/stable/advanced-administration.html#sect.lvm
[lwn-btrfs]: http://lwn.net/Articles/579009/
[quota]: https://btrfs.wiki.kernel.org/index.php/Quota_support
[raphael]: http://raphaelhertzog.com/
[roland]: http://www.gnurandal.com/
[subvolumes]: https://btrfs.wiki.kernel.org/index.php/SysadminGuide#Subvolumes
[wheezy-luks]: /2013/05/primeiros-dias-com-o-debian-wheezy/
