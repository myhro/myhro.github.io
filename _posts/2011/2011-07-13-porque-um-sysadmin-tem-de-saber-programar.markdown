---
date: 2011-07-13 06:52:04
layout: post
title: Porque um sysadmin tem de saber "programar"
...

Conforme lhes disse no post [Detalhes em um Cluster "Beowulf"](http://blog.myhro.info/2011/04/detalhes-em-um-cluster-beowulf/), no [LBC](http://www.ppgcb.unimontes.br/lbc/) todos os alunos cadastrados tem um perfil móvel, podendo se logar em qualquer computador com todos os seus arquivos disponíveis. Como tudo é centralizado, fica muito mais fácil gerenciar o uso de espaço em disco. Apenas analisando a partição "/home" do servidor posso ver quais usuários atingiram o limite de suas quotas ou estão perto de fazê-lo. O engraçado é que o maior vilão nesta história não são os próprios usuários baixando tudo que vêem pela frente, mas sim programas que criam muitos arquivos temporários, como o cache do navegador ou as thumbnails do gerenciador de arquivos.

No Firefox é possível desabilitar facilmente o cache de disco. Mas no Google Chrome (ou [Chromium](http://www.chromium.org/Home), seu irmão livre), mesmo com algumas gambiarras (como abrir o navegador com determinadas opções via linha de comando) não é possível desabilitá-lo completamente. O jeito mais simples acaba sendo utilizar um pequeno Shell-Script que navegue pelas pastas dos usuários, verifique se há arquivos a serem deletados na pasta onde o Google Chrome guarda seu cache e os apague corretamente, como você pode ver a seguir:

{% highlight bash %}
#!/bin/bash

for USUARIO in $(ls /home/); do
    echo "Usuario: $USUARIO"
    if [ -d /home/$USUARIO/.cache/chromium/Cache/ ]; then
        echo "Pasta do Chrome encontrada:"
        echo "/home/$USUARIO/.cache/chromium/Cache/"
        if [ -z $(ls /home/$USUARIO/.cache/chromium/Cache/ | head -n 1) ]; then
            echo "Pasta vazia!"
        else
            rm /home/$USUARIO/.cache/chromium/Cache/*
            echo "Arquivos excluidos."
        fi
    fi
    if [ -d /home/$USUARIO/.cache/chromium/Default/Cache/ ]; then
        echo "Pasta do Chrome encontrada:"
        echo "/home/$USUARIO/.cache/chromium/Default/Cache/"
        if [ -z $(ls /home/$USUARIO/.cache/chromium/Default/Cache/ | head -n 1) ]; then
            echo "Pasta vazia!"
        else
            rm /home/$USUARIO/.cache/chromium/Default/Cache/*
            echo "Arquivos excluidos."
        fi
    fi
done
{% endhighlight %}

Não é um código extenso e não é necessário ser o melhor programador do mundo para desenvolvê-lo. Com algumas poucas semanas em curso de computação você aprenderia a utilizar as estruturas de repetição e condição necessárias. A partir daí basta saber que o "-d" testa se determinado caminho corresponde a um diretório e o "-z" testa se uma string (no caso a primeira linha da lista dos arquivos) está vazia. O problema é que vejo muitas pessoas nestes cursos que abominam a programação e não veem utilidade já que não pretendem trabalhar como programadores.

Há algum tempo li sobre um curso diferente de informática (ao invés do tradicional combo Windows/Word/Excel/Internet) para crianças (ou pré-adolescentes) cuja grade tinha programação em toda a sua extensão. E por que isso? Porque você só domina o computador quando é capaz de ordená-lo a fazer exatamente o que você quer. Ele não é nada mais que isso: uma máquina que recebe e executa ordens de forma repetitiva e sem se cansar. Se você realmente quer ser um profissional "de verdade" em informática, perceba que você não precisa ter determinada habilidade como fim (no caso, a programação), mas tem de saber usá-la em conjunto com outras e realizar seu trabalho de forma mais simples ou mais bem feita. O Shell-Script é o melhor amigo do Administrador de Sistemas Linux.

O agradecimento do post fica para o Prof. [Steve Lacerda](http://www.stevelacerda.net/), quem me ajudou a solucionar um bug chato onde a lista de arquivos retornada no teste do if acabava atrapalhando os comandos seguintes. Por isso a atenção especial com o "head -n 1". Para quem quiser ler mais a respeito, há a apostila "[Introdução ao Shell Script](http://aurelio.net/shell/apostila-introducao-shell.pdf)", do Aurélio Jargas e o já citado guia "[Programando em Shell-script](http://www.devin.com.br/shell_script/)", do Hugo Cisneiros.
