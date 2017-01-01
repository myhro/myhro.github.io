---
date: 2011-05-08 21:42:18
layout: post
title: 'Dica do dia: Screen'
...

O Linux oferece terminais virtuais desde os primórdios de sua existência, mesmo em modo texto. Um simples Ctrl+Alt+Fx (F1 ao F6 são terminais, F7 é o modo gráfico) lhe dá a oportunidade de usar um terminal livre sempre que o primeiro estiver ocupado por conta de algum comando. Você pode até mesmo deixar algum sistema de monitoramento rodando, como o [htop](http://htop.sourceforge.net/), e retornar ao terminal anterior sempre que necessário.

E mais ainda, caso deixar o programa rodando não seja crucial, você pode mandá-lo para background pausando sua execução com um simples CTRL+Z e retornar ao mesmo quando necessário com o comando "fg" ([aqui](http://www.linuxmanpages.com/man1/bash.1.php) você encontra dicas de utilização do "fg" e outros comandos básicos). Seria útil caso você estivesse editando um arquivo e precisasse do caminho correto para alguma pasta, por exemplo.

Mas o que fazer quando não se pode pausar a execução de um processo ou você está acessando uma máquina remotamente via SSH? O "método lusitano" seria simplesmente abrir várias sessões do GNOME-Terminal/Konsole (localmente) ou do [Putty](http://www.chiark.greenend.org.uk/~sgtatham/putty/) (remotamente). Parando um pouco para pensar podemos concluir que resolver o problema na força bruta não é a solução mais elegante.

Um dos utilitários pequenos e funcionais para este tipo de situação é o [Screen](http://www.gnu.org/software/screen/), parte do [Projeto GNU](http://www.gnu.org/gnu/thegnuproject.html). Executá-lo (basta digitar "screen") pela primeira vez pode ser estranho, pois mesmo com sua mensagem de boas-vindas acabamos de volta ao terminal como se nada tivesse acontecido. Na verdade, de agora em diante temos um sistema completo de terminais virtuais à nossa disposição, apenas as teclas de atalho utilizadas serão diferentes.

Estes são seus principais comandos:

**CTRL+A C**: cria uma nova sessão do terminal (create).  
**CTRL+A N**: muda para a próxima sessão (next).  
**CTRL+A P**: muda para a sessão anterior (previous).  
**CTRL+A D**: sai do screen, mantendo suas sessões ativas (detach). 

Pode parecer confuso, mas todos os comandos consistem em dois passos. Primeiro pressionamos as teclas CTRL e A simultaneamente e logo após soltá-las, pressiona-se a tecla referente ao comando desejado. Só tome cuidado ao digitar CTRL+A D, pois se você esquecer do A (digitando apenas CTRL+D) a sessão atual será finalizada (neste caso o comando "exit" faz a mesma coisa). Desta forma você retornará para a sessão anterior e caso nenhuma outra exista, o Screen será encerrado.

A maior utilidade do Screen não é apenas oferecer terminais virtuais e sim manter ativo tudo que você estiver rodando. Ou seja, se você estiver administrando um servidor remotamente e a conexão cair, basta digitar "screen -r" ao se conectar novamente e continuar exatamente de onde estava. Desta forma, qualquer editor de texto que estivesse aberto ou download sendo realizado não seria interrompido.

Você pode até mesmo compartilhar um terminal em tempo real, o que é muito útil para ensinar alguém o que você está fazendo. Após ter aberto uma sessão remotamente, basta pedir pra pessoa do outro lado digitar "screen -x". Assim suas telas serão conectadas e tudo que for digitado de um lado aparecerá no outro instantaneamente.

Um pequeno detalhe que vale a pena ser observado é o seguinte: quando se abre o Screen, ele não lê seus arquivos de configuração do terminal "/etc/profile" ou "~/.profile", <del>apenas o "/etc/bash.bashrc" e o "~/.bashrc"</del>. Correção: a maneira mais fácil de contornar isto é adicionar no arquivo "~/.screenrc" a seguinte linha:

    shell -$SHELL

Pode parecer bobo, mas você pode se perguntar porque o [bash-completion](http://packages.debian.org/squeeze/bash-completion) ou seu "[ls colorido](http://www.vivaolinux.com.br/dica/Mantenha-o-ls-sempre-colorido/)" pararam de funcionar.
