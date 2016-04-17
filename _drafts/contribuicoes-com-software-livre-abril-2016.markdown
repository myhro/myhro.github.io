---
layout: post
title: "Contribuições com Software Livre em Abril de 2016"
categories:
- foss
- relatorio
---

Uma prática muito comum que observo há algum tempo em comunidades de software livre são relatórios mensais sobre suas próprias contribuições. A ideia é informar sobre o que foi feito recentemente, assim como atrair pessoas que possam se interessar em projetos nas mesmas áreas que eu para trabalharem comigo. Sendo assim, seguem minhas contribuições no mês de Abril de 2016.

## Debian

* [Apliquei para o processo][newmaint-application] de me tornar um Debian Maintainer, obtendo o apoio de [Antonio Terceiro][newmaint-terceiro] (o que eu havia pedido) e [Charles Plessy][newmaint-plessy] (o que foi uma agradabilíssima surpresa). A ideia é assumir uma posição mais "oficial" junto ao projeto e, quem sabe, daqui uns tempos me tornar um Debian Developer (com um e-mail `@debian.org`).
* Assumi os bugs ITP (Intent to Package) [#790611][grip-itp] e [#790612][p-and-a-itp], do [Grip][grip] e do [Path-and-Addresss][p-and-a] (dependência do Grip), respectivamente. O Gustavo Panizzo os havia criado no meio do ano passado, mas não teve tempo de terminar o empacotamento. Perguntei se poderíamos trabalhar juntos nisso e ele me permitiu assumir a responsabilidade.
* Comentei no bug [#788527][pyro4-update], reforçando o pedido para que o pacote `python2-pyro4`, dependência do `bootstrap-vz` fosse atualizado, o que foi rapidamente atendido pelo Laszlo Boszormenyi.
* Corrigi alguns problemas no empacotamento do `cloud-utils`, como o [copyright de uma man page][cloud-utils-man-page-copyright] que havia ficado faltando e me foi apontado pelo Chris Lamb. O upload foi patrocinado pelo Antonio Terceiro, que tem me ajudado bastante com o cloud-utils.
* Corrigi o bug [#820607][bvz-changelog-bug], reportado pelo Olivier Berger, relacionado ao changelog do upstream incluído no pacote do `bootstrap-vz` estar praticamente vazio.
* [Corrigi um hífen não-ASCII][non-ascii-hyphen] na página `DebianMaintainer` da Wiki do Debian. Pode parecer bobo, mas isso fez com que minha mensagem de aplicação tivesse sua assinatura invalidada na interface web da lista de e-mail.
* [Desencorajei a entrada de um pacote][series-review] no Debian, o `series`, no bug RFS (Request for Sponsorship) [#820733][series-rfs]. Infelizmente se trata de um software muito recente, praticamente utilizado apenas pelo autor. Não é o tipo de software que deve ser empacotado para o Debian.
* Realizei diversas (muitas mesmo) melhorias no pacote do `bootstrap-vz`. Se tiver trabalhado 20h na semana só com isto ainda foi pouco. A mais importante é [a criação do pacote `bootstrap-vz-doc`][bvz-doc-package], com a documentação em HTML disponível para leitura offline.
* [Reportei alguns pequenos problemas][collab-maint-issues] com o "collab-maint" (repositório para manutenção colaborativa de pacotes), como certificado HTTPS expirado e falta de permissões para configurar corretamente um repositório. Alexander Wirt e Mattia Rizzolo me ajudaram a resolver estes problemas.
* Reportei o bug [#820020][responses-update], solicitando a atualização do pacote `python-responses`, dependência dos testes do `python-path-and-address`, o que foi atendido (muito) rapidamente pelo Ondrej Novy.
* Reportei o bug [#820685][zbackup-improvements], incluindo alguns patches com melhorias no o empacotamento do `zbackup`.
* Resolvi o bug [#821175][grip-conflict-bug], relacionado a um conflito com o diretório de configurações do `grip`. O que acontece é que um pacote do GNOME CD Ripper tinha o mesmo, mas mesmo não faz mais parte do Debian desde 2009 (e foi abandonado pelo upstream em 2005). Sendo assim, não havia nada a ser feito.
* [Revisei o pacote `python-django-gravatar2`][django-gravatar-review], referente ao bug RFS [#819681][django-gravatar-rfs]. Encontrei alguns problemas, mas o Pierre-Elliott resolveu a maioria deles.
* [Revisei a imagem do Debian Jessie 8.4][jessie-84-ec2-review] criada para o Amazon EC2 pelo James Bromberger. Encontrei alguns pequenos problemas, nada grave.
* [Revisei o pacote `netsed`][netsed-review], referente ao bug RFS [#821236][netsed-rfs]. O pacote é mantido pelo Mats Erik Andersson há mais de 5 anos, então não havia nada muito grave a ser corrigido.
* [Revisei o pacote `python-hashids`][hashids-review], referente ao bug RFS [#820900][hashids-rfs]. O empacotamento foi feito por um antigo DD, Edward Betts, então não tive nada a acrescentar.
* [Revisei o pacote `roadfighter`][roadfighter-review] e encontrei pouquíssimos detalhes a serem corrigidos, devido ao grande trabalho do Carlos Donizete Froes.
* Submeti o bug RFS [#820029][grip-rfs] do `grip`, atendido pelo Mattia Rizzolo.
* Submeti o bug RFS [#819773][p-and-a-rfs] do `python-path-and-address`, também atendido pelo Mattia Rizzolo.
* Tive meu bug RFS [#819289][pythonpy-rfs] do `pythonpy`, que havia submetido no final de março, atendido. O upload foi patrocinado pelo Dmitry Shachnev.

## bootstrap-vz

* [Conversei com Anders Ingemann][bvz-release-issue] para realizarmos releases assinados via GPG/PGP.
* Lancei a versão `0.9.10`, [já devidamente assinada][bvz-release-0910].
* [Reportei manifests duplicados do GCE][bvz-gce-manifests], confirmando com o Zach Marano se poderíamos remover um dos arquivos sem impactar no workflow interno do Google.
* [Tentei reproduzir][bvz-vagrant-bug-review] um bug reportado pelo Olivier Berger, relacionado a [problemas na inicialização de imagens criadas para o Vagrant/VirtualBox][bvz-vagrant-bug], sem sucesso.

## Path-and-Address

* [Submeti um Pull Request][p-and-a-pr2] corrigindo a execução da suíte de testes com o Python 3.
* [Submeti um outro PR][p-and-a-pr3] adicionando suporte ao Travis CI.


[bvz-changelog-bug]: https://bugs.debian.org/cgi-bin/bugreport.cgi?bug=820607
[bvz-doc-package]: https://anonscm.debian.org/git/cloud/bootstrap-vz.git/commit/?id=899e841
[bvz-gce-manifests]: https://github.com/andsens/bootstrap-vz/issues/310
[bvz-release-0910]: https://github.com/andsens/bootstrap-vz/releases/tag/v0.9.10
[bvz-release-issue]: https://github.com/andsens/bootstrap-vz/issues/278#issuecomment-209482634
[bvz-vagrant-bug-review]: https://lists.debian.org/debian-cloud/2016/04/msg00017.html
[bvz-vagrant-bug]: https://lists.debian.org/debian-cloud/2016/04/msg00016.html
[cloud-utils-man-page-copyright]: https://anonscm.debian.org/git/collab-maint/cloud-utils.git/commit/?id=253d5b7
[collab-maint-issues]: https://lists.debian.org/debian-mentors/2016/04/msg00008.html
[django-gravatar-review]: https://lists.debian.org/debian-mentors/2016/04/msg00161.html
[django-gravatar-rfs]: https://bugs.debian.org/cgi-bin/bugreport.cgi?bug=819681
[grip-conflict-bug]: https://bugs.debian.org/cgi-bin/bugreport.cgi?bug=821175
[grip-itp]: https://bugs.debian.org/cgi-bin/bugreport.cgi?bug=790611
[grip-rfs]: https://bugs.debian.org/cgi-bin/bugreport.cgi?bug=820029
[grip]: https://github.com/joeyespo/grip
[hashids-review]: https://bugs.debian.org/cgi-bin/bugreport.cgi?bug=820900#15
[hashids-rfs]: https://bugs.debian.org/cgi-bin/bugreport.cgi?bug=820900
[jessie-84-ec2-review]: https://lists.debian.org/debian-cloud/2016/04/msg00006.html
[netsed-review]: https://bugs.debian.org/cgi-bin/bugreport.cgi?bug=821236#10
[netsed-rfs]: https://bugs.debian.org/cgi-bin/bugreport.cgi?bug=821236
[newmaint-application]: https://lists.debian.org/debian-newmaint/2016/04/msg00001.html
[newmaint-plessy]: https://lists.debian.org/debian-newmaint/2016/04/msg00006.html
[newmaint-terceiro]: https://lists.debian.org/debian-newmaint/2016/04/msg00004.html
[non-ascii-hyphen]: https://wiki.debian.org/DebianMaintainer?action=diff&rev1=140&rev2=141
[p-and-a-itp]: https://bugs.debian.org/cgi-bin/bugreport.cgi?bug=790612
[p-and-a-pr2]: https://github.com/joeyespo/path-and-address/pull/2
[p-and-a-pr3]: https://github.com/joeyespo/path-and-address/pull/3
[p-and-a-rfs]: https://bugs.debian.org/cgi-bin/bugreport.cgi?bug=819773
[p-and-a]: https://github.com/joeyespo/path-and-address
[pyro4-update]: https://bugs.debian.org/cgi-bin/bugreport.cgi?bug=788527#10
[pythonpy-rfs]: https://bugs.debian.org/cgi-bin/bugreport.cgi?bug=819289
[responses-update]: https://bugs.debian.org/cgi-bin/bugreport.cgi?bug=820020
[roadfighter-review]: https://lists.debian.org/debian-devel-portuguese/2016/04/msg00003.html
[series-review]: https://lists.debian.org/debian-mentors/2016/04/msg00264.html
[series-rfs]: https://bugs.debian.org/cgi-bin/bugreport.cgi?bug=820733
[zbackup-improvements]: https://bugs.debian.org/cgi-bin/bugreport.cgi?bug=820685
