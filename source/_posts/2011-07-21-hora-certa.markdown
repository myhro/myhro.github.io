---
date: 2011-07-21 21:33:30
layout: post
title: Hora certa
categories:
- ntp
- python
---

Sempre gostei de sincronizar meus relógios. Ainda criança, costumava esperar o relógio da secretaria da escola terminar uma volta completa só para ter certeza que até os segundos do meu relógio estariam em sincronia com o mesmo. Quando descobri o serviço "130 - Hora Certa", gastei muitos pulsos telefônicos (naquela época a tarifação ainda não era baseada em minutos) com o mesmo intuito. Algum tempo depois descobri que era possível fazer isto de graça utilizando sites como o "[Hora certa!](http://www.horacerta.com.br/)" ou o "[Hora Legal Brasileira](http://pcdsh01.on.br/HoraLegalBrasileira.asp)", embora continuasse sendo um trabalho manual.

Quando aprendi que o processo poderia ser automatizado minha vida mudou. Seguindo os passos do [Guia Rápido](http://ntp.br/NTP/MenuNTPGuia) do [NTP.br](http://ntp.br/) (há vídeos na página para simplificar a explicação), em poucos minutos você pode configurar praticamente qualquer sistema operacional para acertar seu relógio automaticamente, junto ao [Observatório Nacional](http://www.on.br/), com precisão na casa dos centésimos de segundo. Isto foi de suma importância algum tempo depois, quando realizei a configuração inicial do cluster do [LBC](http://www.ppgcb.unimontes.br/lbc). Algo que esqueci de citar no post [Detalhes em um Cluster "Beowulf"](http://blog.myhro.info/2011/04/detalhes-em-um-cluster-beowulf/) é justamente a necessidade do [NTP](http://en.wikipedia.org/wiki/Network_Time_Protocol) neste tipo de aplicação.

O problema é que na [universidade onde estudo](http://www.unimontes.br/) algumas portas de saída (tanto TCP quanto UDP) são bloqueadas. A porta padrão do NTP, a 123, é uma delas. Nunca entendi o porque, mas por se tratar de um protocolo de suma importância (pelo menos para o LBC) não dificultaram sua liberação. Estaria tudo resolvido se a liberação tivesse sido realizada em toda a rede da universidade e não apenas em um laboratório. Vejo outros computadores pertencentes à outros setores e/ou laboratórios com a hora errada constantemente. Inclusive, só posso sincronizar o relógio do meu notebook quando estou na rede do laboratório.

Enquanto o uso do NTP não é possível em toda a rede da universidade, criei o perfil [@NTPUnimontes](http://twitter.com/NTPUnimontes) no Twitter. Em intervalos de 30 minutos a hora certa é postada a partir do servidor do LBC. A única dificuldade enfrentada em sua implementação foi entender como funciona o sistema de [OAuth](http://en.wikipedia.org/wiki/OAuth). De posse das chaves corretas o restante foi trivial, graças às facilidades do [Python Twitter](http://code.google.com/p/python-twitter/) (que por sua vez utiliza o [oauth2](https://github.com/simplegeo/python-oauth2)), uma biblioteca muito simples e funcional.

É claro que o serviço não substitui a utilização do NTP, muito menos o faz de forma automática. Na verdade é apenas uma maneira de informar a hora certa, lembrando sua importância. Quem sabe algum dia tenhamos o "ntp.unimontes.br", o servidor de tempo oficial do Norte de Minas.
