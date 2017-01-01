---
date: 2016-01-25 01:34:23
layout: post
title: "Desligando a tela ao bloquear o Xfce"
...

Como quem acompanha o blog sabe, utilizo exclusivamente Linux no desktop [há quase quatro anos][myhro-ports]. Optei por muito tempo pelas versão estáveis do Debian (Squeeze/Wheezy na época) no notebook, passando a utilizar o Xubuntu (versões LTS, 12.04 e 14.04, com suporte extendido, mas de apenas três anos invés de cinco, diferente do Ubuntu) no computador de trabalho. Considerando a usabilidade do Xubuntu maior do que a do Debian com Xfce, foi natural que passasse a utilizar o primeiro também no meu notebook pessoal.

Questiono a usabilidade do Debian porque é necessário configurar manualmente muitas das coisas que já vem habilitadas por padrão no Xubuntu. Coisas que podem parecer bobas, como o "clique com toque" e "clique com o botão direito, com toque de dois dedos" no touchpad fazem muita diferença. E, tendo voltado pro Debian (Jessie, [a atual versão estável][debian-releases]) recentemente, um destes detalhes voltou a me irritar profundamente: o fato da tela não ser desligada quando bloqueio o sistema (exigindo senha para utilizá-lo novamente).

Como não poderia deixar de ser, se você procurar por isso encontrará soluções esdrúchulas, como a recomendação de se criar [um script que chama o comando de salvar a tela, espera um tempo e chama outro comando para desligá-la depois][xfce-lock-script]. E o pior de tudo, é que como se já não bastasse você precisar de um script para fazer isto em pleno ano de 2016, ele simplesmente não funciona! Por algum motivo a tela liga sozinha pouco tempo depois.

Felizmente, há uma solução simples, ainda que não tão intuitiva, para o problema. Por padrão, o Debian (pelo menos com Xfce) utiliza o [XScreenSaver][xscreensaver] para gerenciamento de proteção de tela, e ele é o responsável por desligá-la após bloquear a sessão. Acessando sua configuração através do menu "Settings -> Screensaver" (ou acessando a opção "Screensaver" do "Settings Manager"), é necessário escolher as seguintes opções:

![](/images/2016/xscreensaver-1.png)

* **Mode**: Blank Screen Only;
* **Lock Screen After**: esta opção não é obrigatória e não irá influenciar quando a tela for bloqueada propositalmente, apenas após o computador ter ficado inativo pelo tempo configurado nas opções anteriores.

![](/images/2016/xscreensaver-2.png)

E o problema todo está nesta última tela. Note que há a opção **Quick Power-off in Blank Only Mode**, que é exatamente o que estávamos procurando. Entretanto, a mesma depende da opção **Power Management Enabled** estar marcada, muito embora ela fique disponível para ser ativada/desativada, estando a opção anterior selecionada ou não! Meu professor de "Interface Homem-Máquina", [Frederico Bida][lattes-bida], provavelmente infartaria vendo uma coisa dessas.

Como pode ser percebido, deixei as demais opções de energia em 1440 minutos (24 horas), simplesmente porque não quero que o XScreenSaver controle quando o computador deve ser suspenso/desligado, já que isto fica a cargo do gerenciador de energia do Xfce. A intenção é que ele se preocupe apenas com o que realmente interessa, que é o fato de desligar corretamente a tela quando a mesma for bloqueada.


[debian-releases]: https://wiki.debian.org/DebianReleases#Production_Releases
[lattes-bida]: http://buscatextual.cnpq.br/buscatextual/visualizacv.do?id=K4234300U5
[myhro-ports]: /2012/08/backports-alternativo-para-o-debian-myhro-ports/
[xfce-lock-script]: https://confluence.clazzes.org/display/KH/HowTo+force+screen+backlight+off+when+locking+screen,+general+and+Xfce4
[xscreensaver]: http://www.jwz.org/xscreensaver/
