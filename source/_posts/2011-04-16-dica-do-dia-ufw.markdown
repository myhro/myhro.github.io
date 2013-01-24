---
date: 2011-04-16 16:21:37
layout: post
title: 'Dica do dia: UFW'
categories:
- firewall
- linux
---

Estava marcado para o mês passado na universidade qual estudo um mini-curso (de 8 horas) sobre IPTables que acabou não sendo realizado. Alguns colegas me perguntaram se eu não tinha interesse em participar e eu disse que não. Perguntaram se eu "já sabia IPTables" e eu disse que sim. O mais curioso não foi a minha recusa e sim a cara de espanto que fizeram ao ouvirem esta afirmação.

É aqui onde entra a experiência, sendo ela profissional ou não. Pessoalmente tenho contato com Linux desde dezembro de 2001 (quase dez anos) e profissionalmente desde 2009. Atualmente administro pelo menos 5 servidores (isso sem incluir o meu routeador, que [também roda Linux](http://www.dd-wrt.com/)) e diversas outras estações que totalizam pelo menos 30 máquinas. Todas rodando Linux. Fica implícito que eu tenho de saber como cuidar do funcionamento do sistema como um todo. O que inclui redes, que por sua vez incluir firewalls.

Se sou um "guru" do IPTables? É evidente que não. Mas desde que você saiba o que é IP, TCP, UDP, pra onde vai um pacote de entrada ou de saída e quais são as portas utilizadas pelas aplicações quais o sistema faz uso, você está apto a configurar um firewall minimamente funcional. As regras do IPTables por si só são bem explicativas ([este resumo](http://www.hardware.com.br/dicas/resumo-iptables.html) é um bom ponto de partida), o que torna a curva de aprendizado completamente trivial.

A partir daí só depende de você. Talvez este conjunto mínimo de regras contido no resumo seja suficiente para todas as suas aplicações (o script de firewall do servidor qual hospeda este blog tem algo em torno de 10 linhas). Quando você precisar de mais que isso, basta pesquisar. O que não falta hoje é documentação.

Com dois comandos (mais um para carregar o módulo) é possível compartilhar sua conexão via NAT:

    # modprobe iptable_nat
    # iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE
    # echo 1 > /proc/sys/net/ipv4/ip_forward

E você pode perceber que apenas um comando é realmente uma regra do IPTables.

Mesmo assim, nem lidar diretamente com o IPTables você precisa. Existem ferramentas que podem ajudar a configurar um firewall de forma mais simples ainda. Se você utiliza Linux no desktop, o que desencorajo fortemente, você pode utilizar o [Firestarter](http://www.hardware.com.br/livros/redes/firestarter.html). Foi justamente a facilidade do Firestarter que me fez procurar algo parecido para ser usado em linha de comando (leia-se servidores sem interface gráfica). E nesta busca encontrei o [UFW](https://launchpad.net/ufw) ([Uncomplicated Firewall](https://help.ubuntu.com/community/UFW)), que além da linha de comandos também tem interface gráfica ([GUFW](http://www.hardware.com.br/guias/ubuntu/firestarter-gufw.html)).

Uma regra simples, para liberar a porta utilizada pelo SSH no IPTables seria:

    # iptables -A INPUT -p tcp --dport 22 -j ACCEPT

Mas com o UFW ela se torna apenas:

    # ufw allow 22/tcp

Não é necessário nem mesmo utilizar o "/tcp" no comando. Você poderia substituir estas duas regras (normalmente utilizadas em conjunto com um [DNS cacher/forwarder](http://www.thekelleys.org.uk/dnsmasq/doc.html), que escuta na porta 53 tanto TCP quanto UDP):

    # iptables -A INPUT -p tcp --dport 53 -j ACCEPT
    # iptables -A INPUT -p udp --dport 53 -j ACCEPT

Por apenas:

    # ufw allow 53

Não tenho nada contra os cursos e mini-cursos sobre assuntos específicos que daqui e dali são ministrados. Eu mesmo penso em realizar algum qualquer dia desses. Você pode ser o tipo de pessoa que realmente dependa de alguém ensinando para conseguir aprender e pode nem mesmo ser sua culpa. Mas na área de TI como um todo, não há qualidade melhor do que ser curioso.

Pesquise, leia, fuce, aprenda. Repita os passos 1 ao 3 quantas vezes for necessário. Se mesmo assim você não conseguir aprender, aí sim procure ajuda de alguém que já sabe. Só não desista antes mesmo de tentar. Ou pior ainda, antes mesmo de procurar saber do que se trata.
