---
date: 2011-04-11 23:46:21
layout: post
slug: detalhes-em-um-cluster-beowulf
title: Detalhes em um Cluster "Beowulf"
categories:
- cluster
- linux
---

Um dos principais objetivos do [Laboratório de Biologia Computacional](http://www.ppgcb.unimontes.br/course/view.php?id=27), antes mesmo da sua criação, foi utilizá-lo como um centro de computação distribuída. Muitos projetos do Mestrado em Ciências Biológicas envolvem estatística e análise de dados, utilizando para isto a [linguagem de programação R](http://en.wikipedia.org/wiki/R_%28programming_language%29). Felizmente a tarefa de realizar a implementação do cluster foi destinada à mim.

Todos os sistemas do laboratório são baseados em Linux e [FOSS](http://en.wikipedia.org/wiki/Free_and_open_source_software) em geral. O próprio R é um destes projetos gigantescos (só no [CRAN](http://cran.r-project.org/) (Comprehensive R Archive Network) são quase 3000 pacotes) distribuídos sobre licença GPL. Não seria surpresa nenhuma constatar que há um pacote chamado "[Rmpi](http://cran.r-project.org/web/packages/Rmpi/index.html)" que fornece uma interface para processamento paralelo. A partir disto, a saída mais simples foi implementar um cluster "[Beowulf](http://www.ibiblio.org/pub/linux/docs/HOWTO/archive/Beowulf-HOWTO.html#ss2.2)", que tem como filosofia a utilização de computadores domésticos (baratos) e software livre.

O propósito de um cluster é funcionar como se todos os computadores fosse um só. Ou seja, a entrada dos dados é feita em um computador, o processamento é feito por todos os computadores em conjunto e a saída é realizada no computador qual realizou a entrada. Para isto seria preciso que o usuário qual faz a requisição e os arquivos a serem processados fossem os mesmos em todos os computadores. A maneira mais fácil de fazer isto é centralizando os logins e arquivos dos usuários.

Para centralizar os logins, poderia ser utilizado qualquer sistema de serviço de diretório, como o [NIS](http://www.tldp.org/HOWTO/NIS-HOWTO/glossary.html#AEN120) ou o [LDAP](http://www.gracion.com/server/whatldap.html) (escolhi o NIS por sua simplicidade). Para centralizar os arquivos dos usuários, basta compartilhar a pasta "/home" via [NFS](http://nfs.sourceforge.net/nfs-howto/ar01s02.html#whatis_nfs) e montá-la em cada um dos nós. Além disso, é necessário que este usuário possa se logar em todos os computadores a partir de qualquer computador sem utilizar senha. O propósito disto é que todo o processo é automatizado e não faria sentido algum ter de digitar a senha do usuário cada vez que fosse necessário efetuar alguma troca de dados entre os computadores.

A documentação que utilizei como base para implementação do NIS + NFS foi o artigo "[Configurando NFS + NIS (Ubuntu)](http://www.vivaolinux.com.br/artigo/Configurando-NFS-+-NIS-%28Ubuntu%29/)". Sendo o Ubuntu um sistema Debian-based, a necessidade de adaptação do guia para a utilização no laboratório (que tem o Debian instalado em todos os computadores) foi zero. Para a configuração do sistema de processamento distribuído em si, utilizando o [MPICH2](http://www.mcs.anl.gov/research/projects/mpich2/), me baseei no artigo "[Setting Up an MPICH2 Cluster in Ubuntu](https://help.ubuntu.com/community/MpichCluster)". Com relação ao login sem senha, [este trecho](http://www.hardware.com.br/tutoriais/dominando-ssh/pagina5.html) do guia "Dominando o SSH", do sempre excelente Carlos E. Morimoto sobre chaves de autenticação descreve o processo de forma bem simples.

Vale ressaltar que as vezes pode não ser possível utilizar SSH sem senha se o seu arquivo "authorized_keys" fica localizado em uma pasta montada remotamente (que é exatamente o que acontece neste caso). O propósito primordial do SSH é a segurança e até mesmo as permissões dos arquivos podem impedir um login (a forma como uma partição foi montada é um exemplo disto, como veremos a seguir). A maneira mais simples de contornar este pequeno empecilho é deixar o arquivo em "/etc/ssh/authorized_keys", por exemplo, e especificar a mudança no seu "[sshd_config](http://linux.die.net/man/5/sshd_config)".

Todos os guias utilizados são extremamente didáticos. Se tem uma coisa boa que o Ubuntu (e sua enorme comunidade) trouxe ao universo Linux, foi a abundância de documentação simples, clara e objetiva. O Ubuntu foi feito para ser utilizado por seres humanos, e no quesito "facilidade de acesso à informação", ele o faz magistralmente.

O único problema é que no artigo sobre a configuração do NIS e NFS, o autor recomenda a montagem da partição no "/etc/fstab" com os parâmetros "rw,bg,tcp,rsize=32768,wsize=32768,hard,nointr,nolock,noac,timeo=600,user,auto". Irá funcionar corretamente desta maneira? Sim, para o propósito de simplesmente compartilhar pastas remotamente irá. Mas para o MPICH não. Ao montar a partição com estes parâmetros, erros como o seguinte podem ocorrer:

    problem with execution of /home/mpiu/hellow on node-1:
    [Errno 13] Permission denied

Perdi pelo menos 3 horas da minha vida até entender porque isto estava acontecendo. A pasta montada remotamente pertence ao usuário, logo, não seria isto que estava ocasionando o problema das permissões. Mais intrigante ainda foi o fato que eu havia testado não uma, mas duas vezes (em casa e no laboratório) a instalação desta maneira e havia funcionado corretamente.

Porém, me lembrei de um detalhe. Quando configurei e testei o sistema eu havia montado as partições na hora (com um simples "mount ip.do.servidor:/home /home"), já que não era definitivo e não havia porque adicionar a partição no "/etc/fstab" naquele momento.

Qual teste eu fiz? Desmontei a partição e a montei novamente, com este mesmo comando. Problema resolvido. Tudo voltou a funcionar como estava no dia em que testei pela primeira vez. Com esta constatação, bastou trocar todos aqueles doze parâmetros anteriores por um só: "defaults". Qualquer semelhança com a "[Navalha de Occam](http://pessoas.hsw.uol.com.br/occams-razor.htm)" é mera coincidência.

Minha linha referente a "/home" no "/etc/fstab" ficou da seguinte maneira:

    10.0.0.1:/home    /home    nfs        defaults    0    0

Problema resolvido. Hoje poderei dormir despreocupado.
