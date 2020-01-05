---
date: 2015-08-12 23:43
layout: post
title: "Limpando um dispositivo com o wipefs"
...

Havia um tempo em que copiar a instalação de um sistema operacional para um pendrive não era uma tarefa trivial. No caso do Windows era necessário [recriar as partições e reescrever o setor de boot][windows-manual-usb] do dispositivo USB manualmente - algo que só foi facilitado com o lançamento do [Windows USB/DVD Download Tool][windows-usb-tool]. No mundo Linux, os utilitários [UNetbootin][unetbootin] e [Universal USB Installer][pendrive-linux] faziam este papel.

Com o lançamento do Debian Squeeze (6.0), no começo de 2011, me recordo que uma das coisas que considerei mais interessantes era a introdução das "[ISO híbridas][hybrid-iso]", isto é, que poderiam tanto serem gravadas em CD ou DVD quanto facilmente copiadas para um pendrive. Ao invés de se recorrer a softwares criados exclusivamente com este objetivo, bastava então realizar uma cópia bit-a-bit, com ferramentas como `dd` ou `cat` que o resultado seria o mesmo.

Como tudo na vida tem um porém, este processo não é indolor para o pobre pendrive. Por ser necessário reescrever o setor de boot, não é possível copiar a ISO para uma partição, somente para o dispositivo inteiro. O que acontece é que, devido a um processo tão drástico, quando é necessário formatar o pendrive para utilizá-lo novamente como mídia de armazenamento removível, não basta recriar a tabela de partições e formatar com o sistema de arquivos desejado. Desta forma o label dele (o nome que aparece no gerenciador de arquivos) continuará sendo o da última ISO que foi copiada.

Passei literalmente anos convivendo com isto, sendo indagado corriqueiramente por alguns curiosos sobre o porque do meu pendrive levar o nome do Debian ou do Xubuntu. Até que um dia, <s>em um artigo que infelizmente não lembro o nome para poder linkar aqui</s> no artigo [bcache and/vs. LVM cache][bcache-lvm-cache], conheci o [wipefs][wipefs]. Sua principal funcionalidade, como o próprio nome sugere, é apagar assinaturas de sistemas de arquivos em um dispositivo de armazenamento. Desde então, com um simples `wipefs -a /dev/sdX`, um incômodo de tanto tempo pôde ser resolvido.

[bcache-lvm-cache]: http://blog-vpodzime.rhcloud.com/?p=45
[hybrid-iso]: http://joeyh.name/blog/entry/Debian_USB_install_from_hybrid_iso/
[pendrive-linux]: http://www.pendrivelinux.com/universal-usb-installer-easy-as-1-2-3/
[unetbootin]: http://unetbootin.github.io
[windows-manual-usb]: http://www.intowindows.com/how-to-install-windows-7vista-from-usb-drive-detailed-100-working-guide/
[windows-usb-tool]: https://www.microsoft.com/en-us/download/windows-usb-dvd-download-tool
[wipefs]: http://karelzak.blogspot.com/2009/11/wipefs8.html
