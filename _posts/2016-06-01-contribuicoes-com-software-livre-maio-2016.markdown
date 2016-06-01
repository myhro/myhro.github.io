---
date: 2016-06-01 05:26:33
layout: post
title: "Contribuições com Software Livre em Maio de 2016"
categories:
- foss
- relatorio
---

Uma prática muito comum que observo há algum tempo em comunidades de software livre são relatórios mensais sobre suas próprias contribuições. A ideia é informar sobre o que foi feito recentemente, assim como atrair pessoas que possam se interessar em projetos nas mesmas áreas para trabalharem comigo. Sendo assim, seguem minhas contribuições no mês de Maio de 2016.

## Debian

* [Atualizei o backport][cloud-utils-backport-update] do pacote `cloud-utils`, após o [Vivek Das Mohapatra ter reportado][cloud-utils-sfdisk-bug] que os problemas com o `sfdisk` persistiam na versão até então disponível no `jessie-backports`.
* [Atualizei o pacote][cloud-utils-update] `cloud-utils` para a nova versão liberada pelo upstream (depois de quase três anos sem releases oficiais).
* [Atualizei o pacote][grip-update] `grip`, evitando que o mesmo utilizasse o diretório destinado para módulos Python, visto que se trata de uma aplicação de linha de comando.
* [Atualizei o pacote][pythonpy-update] `pythonpy`, fazendo com o script de bash completion utilize o executável interno, ao invés de colocá-lo junto com os demais no diretório `/usr/bin/`.
* Como primeira tarefa pelo time de módulos Python, [preparei uma atualização][python-social-auth-update] do pacote `python-social-auth`. Por conta de alguns detalhes, como a inclusão de arquivos CSS e JavaScript do [Twitter Bootstrap][bootstrap], o upload do mesmo ainda não foi realizado (mas [já estou trabalhando nisso][python-social-auth-repack]).
* Como primeira tarefa pelo time de Ruby, [atualizei o pacote][ruby-yajl-update-1] `ruby-ffi-yajl` para a última versão do upstream. Cometi um pequeno equívoco e tive de [enviar uma retificação][ruby-yajl-update-2], revertendo a conversão da documentação de Markdown para HTML. Isto faz sentido quando a documentação está separada em diversos arquivos/seções, mas no caso de um só arquivo é desnecessário.
* Enviei [meu primeiro patch][debian-cd-html] para o [Debian CD Team][debian-cd-team], corrigindo o cabeço de algumas páginas que não estavam fechando corretamente a tag `<a>` no HTML.
* Ingressei no [Debian Python Modules Team (DPMT)][debian-dpmt] e no [Debian Ruby Team][debian-ruby]. A intenção é ter acesso de escrita aos repositórios dos pacotes mantidos por estes dois times.
* Tive [minha aplicação para me tornar um Debian Maintainer aceita][debian-newmaint-accepted]. Agora só preciso esperar a publicação da minha chave na próxima atualização do pacote `debian-keyring`.

## bootstrap-vz

* Triei um bug sobre o [log da inicialização do sistema não estar sendo enviado para o console do KVM][bvz-console-bug]. A parte mais interessante foi que isso me levou a descobrir que sim, KVM e VirtualBox podem ser instalados na mesma máquina - apenas não podem ser utilizados ao mesmo tempo.


[bootstrap]: https://getbootstrap.com/
[bvz-console-bug]: https://github.com/andsens/bootstrap-vz/pull/320
[cloud-utils-backport-update]: https://lists.debian.org/debian-backports-changes/2016/05/msg00096.html
[cloud-utils-sfdisk-bug]: https://lists.debian.org/debian-cloud/2016/05/msg00014.html
[cloud-utils-update]: https://lists.debian.org/debian-devel-changes/2016/05/msg02129.html
[debian-cd-html]: https://lists.debian.org/debian-cd/2016/05/msg00057.html
[debian-cd-team]: https://wiki.debian.org/Teams/DebianCd
[debian-dpmt]: https://wiki.debian.org/Teams/PythonModulesTeam
[debian-newmaint-accepted]: https://bugs.debian.org/cgi-bin/bugreport.cgi?bug=821296#10
[debian-ruby]: https://wiki.debian.org/Teams/Ruby
[grip-update]: https://lists.debian.org/debian-devel-changes/2016/05/msg01957.html
[python-social-auth-repack]: https://lists.debian.org/debian-python/2016/05/msg00077.html
[python-social-auth-update]: https://lists.debian.org/debian-python/2016/05/msg00061.html
[pythonpy-update]: https://lists.debian.org/debian-devel-changes/2016/05/msg02207.html
[ruby-yajl-update-1]: https://lists.debian.org/debian-devel-changes/2016/05/msg00549.html
[ruby-yajl-update-2]: https://lists.debian.org/debian-devel-changes/2016/05/msg00787.html
