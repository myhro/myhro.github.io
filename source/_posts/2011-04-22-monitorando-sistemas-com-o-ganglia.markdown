---
date: 2011-04-22 11:23:29
layout: post
slug: monitorando-sistemas-com-o-ganglia
title: Monitorando sistemas com o Ganglia
categories:
- ganglia
- linux
---

O [Ganglia](http://ganglia.sourceforge.net/) é uma maravilhosa ferramenta de monitoramento que serve tanto para clusters de milhares de máquinas quanto para um só computador. Ele possui uma interface web com gráficos gerados pelo [RRDtool](http://www.mrtg.org/rrdtool/), criado por [Tobias "Tobi" Oetiker](http://tobi.oetiker.ch/vita.html).

Seu funcionamento se divide em dois módulos: o "Ganglia Monitoring Daemon" (gmond) e o "Ganglia Meta Daemon" (gmetad). O gmond reside em cada um dos nós a serem monitorados, gerando os dados relacionados ao uso de CPU, memória, rede e disco. O gmetad normalmente fica no servidor web, agrupando os dados provenientes dos nós. Seu esquema de funcionamento está representado no diagrama a seguir:

{% img center /images/2011/ganglia-single.gif %}

É possível monitorar vários clusters em uma só página dividindo-os em sub-grupos de acordo a tarefa desempenhada, como você pode ver no [Wikimedia Cloud Report](http://ganglia.wikimedia.org/). O próximo diagrama representa sua utilização em um grupo de clusters:

{% img center /images/2011/ganglia-cluster.gif %}

A configuração do Ganglia não é muito difícil, mas pode ser um tanto quanto confusa em um primeiro momento. Em uma instalação do Debian "Squeeze", por exemplo, você instalaria apenas o pacote "ganglia-monitor" em cada um dos nós e os pacotes "ganglia-monitor", "gmetad" e "ganglia-webfrontend" (que por sua vez instalaria o Apache, o PHP e o RRDtool como dependências) no servidor qual disponibilizaria a página com os dados de monitoramento referentes ao cluster. Para a interface web se tornar acessível a partir da URL http://servidor/ganglia/, basta adicionar a linha "Alias /ganglia /usr/share/ganglia-webfrontend" na configuração do seu VirtualHost (o default, se for o caso) ou copiar o arquivo de configuração (que contém apenas esta mesma linha) para a pasta de configurações do Apache e em seguida recarregar as configurações do serviço.

    # cp /etc/ganglia-webfrontend/apache.conf /etc/apache2/conf.d/ganglia.conf
    # /etc/init.d/apache2 reload

Se você estiver monitorando apenas uma máquina, nenhuma configuração adicional é realmente necessária. Você poderia editar nome do cluster (que na verdade é um computador só) a ser exibido na página do Ganglia no arquivo "/etc/ganglia/gmond.conf" no bloco "cluster", que contém os parâmetros "name", "owner", "latlong" e "url". No arquivo "/etc/ganglia/gmetad.conf" você precisaria modificar o parâmetro "data\_source", trocando "my cluster" pelo nome desejado. Em seguida, ao reiniciar os serviços "ganglia-monitor" e "gmetad" você poderá acompanhar a utilização dos recursos da máquina em tempo "quase" real.

Para monitorar duas ou mais máquinas, você precisa configurar a forma de troca dos dados entre elas. Por padrão, o Ganglia utiliza multicast para isto e em alguns ambientes pode ser possível utilizá-lo sem realizar nenhuma modificação adicional. Em outros casos é preciso aumentar o TTL dos pacotes multicast ou definir uma rota estática. Às vezes isto pode nem mesmo ser possível, como no [caso do Amazon EC2](http://blog.kenweiner.com/2010/10/monitor-hbase-hadoop-with-ganglia-on.html). Você pode ver detalhes a respeito da segunda situação [nesta página](http://www.msg.ucsf.edu/local/ganglia/ganglia_docs/install.html).

Creio que a forma mais simples é utilizar unicast ao invés de multicast. Basta adicionar as seguintes linhas ao arquivo "/etc/ganglia/gmond.conf" (trocando o "10.0.0.1" pelo IP do servidor) de cada nó:

    udp_send_channel {
        host = 10.0.0.1
        port = 8666
        ttl = 1
    }

E em seguida modificar o parâmetro "send\_metadata\_interval" no mesmo arquivo, trocando o "0" por um valor maior. O [FAQ oficial do Ganglia](http://sourceforge.net/apps/trac/ganglia/wiki/FAQ) recomenda 15 segundos. Em seguida, insira estas outras linhas no arquivo "/etc/ganglia/gmond.conf" do servidor qual monitora o cluster inteiro:

    udp_recv_channel {
        port = 8666
    }

Não é necessário remover ou comentar as rotas multicast já existentes pois as versões mais recentes do Ganglia suportam vários canais simultâneos.

Por fim, para separar os clusters em grids (como na página do [Wikimedia Cloud Report](http://ganglia.wikimedia.org/)), você precisaria de duas ou mais instâncias do "gmetad". Cada uma contaria com um parâmetro "gridname" diferente e o servidor web qual reúne todos os dados teria dois ou mais parâmetros "data\_source" referentes a cada uma das instâncias (incluindo o "localhost" já que ele normalmente também roda o "gmetad").

Referências:  
1. [IBM developerWorks: Wikis - Systems - ganglia](http://www.ibm.com/developerworks/wikis/display/WikiPtype/ganglia)  
2. [Server Fault: Ganglia without multicast](http://serverfault.com/questions/22269/ganglia-without-multicast)
