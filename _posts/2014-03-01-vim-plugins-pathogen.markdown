---
date: 2014-03-01 16:17
layout: post
title: "Vim + Plugins = Pathogen"
categories: 
- pathogen
- vim
---

A escolha de um editor decente e universal não é fácil. Antes de começar a utilizar o [Vim](http://www.vim.org/) corriqueiramente, passei por diversos outros como:

* Bloco de notas: Um editor sincero. Não espere nada além de realmente editar textos.
* [Joe](http://joe-editor.sourceforge.net/): Muito bacana. Já havia decorado boa parte dos seus milhares de atalhos.
* [nano](http://www.nano-editor.org/): É sério que alguém ainda usa isso? Já implementaram o "_undo_", pelo menos?
* [Notepad++](http://notepad-plus-plus.org/): Um editor acima da média, mas _Windows-only_.

Todos eles tinham pelo menos um ou vários desses problemas:

* Não funcionava sem ambiente gráfico (modo texto).
* Não possuía _syntax highlighting_.
* Não sabia lidar com quebras de linha de diferentes sistemas operacionais (CR+LF vs. LF).
* Não salvava em [UTF-8](http://www.utf8everywhere.org/).
* Não trocava TAB por _n_ espaços.

E ainda havia o mais grave de todos: não estavam disponíveis em todas as máquinas quais utilizo. Foi somente a partir deste momento, que não deve ter acontecido há muito mais do que dois anos atrás, que decidi de vez por utilizar o Vim sempre que fosse necessário editar qualquer arquivo texto que fosse. Sempre que logo em uma máquina qual não estou familiarizado, tenho a certeza (se é que podemos ter certeza de algo nessa vida) de que pelo menos o comando `vi` (que na verdade faz parte do pacote [vim-tiny](http://packages.debian.org/sid/vim-tiny), sendo uma versão menor e simplificada do Vim) estará disponível.

Acontece que utilizar apenas o editor puro nem sempre é o suficiente. Uma parte considerável das surpresas no estilo "_Wow! O Vim faz isso?!_" é proporcionada pelos [plugins](http://www.vim.org/scripts/) desenvolvidos por terceiros. Entretanto, além de muitos deles possuírem passos de instalação crípticos, é complicado manter todos devidamente atualizados. Foi a partir daí que passei a usar o [Tim Pope](http://tpo.pe/)'s [pathogen.vim](https://github.com/tpope/vim-pathogen), em um daqueles momentos onde sempre me pergunto: "_Por que é que não fiz isso antes?!_".

Após instalar o Pathogen, o que consiste basicamente em salvar o script em `~/.vim/autoload/pathogen.vim` e adicionar a linha `execute pathogen#infect()` ao seu `~/.vimrc`, basta criar o diretório `~/.vim/bundle/` e simplesmente clonar lá o repositório (muitos estão no [GitHub](https://github.com/)) do plugin qual deseja utilizar. A grande maioria dos plugins estará disponível a partir deste processo, mas alguns, mais complexos, podem exigir a adição de algumas informações no arquivo `~/.vimrc`.

Por exemplo, para instalar o [Syntastic](https://github.com/scrooloose/syntastic), um plugin que verifica seu código a cada vez que o arquivo é salvo, indicando possíveis erros quais serão encontrados na compilação ou quando for interpretado, suportando dezenas de linguagens de programação, basta realizar os seguintes passos:

    [myhro@wheezy:~]$ cd ~/.vim/bundle/
    [myhro@wheezy:~/.vim/bundle]$ git clone https://github.com/scrooloose/syntastic.git
    Cloning into 'syntastic'...
    remote: Reusing existing pack: 9852, done.
    remote: Counting objects: 40, done.
    remote: Compressing objects: 100% (39/39), done.
    remote: Total 9892 (delta 15), reused 0 (delta 0)
    Receiving objects: 100% (9892/9892), 2.48 MiB | 247 KiB/s, done.
    Resolving deltas: 100% (4773/4773), done.
    [myhro@wheezy:~/.vim/bundle]$ ls
    syntastic
    [myhro@wheezy:~/.vim/bundle]$ 

E pronto. O Syntastic está instalado. Para atualizá-lo, bastará entrar em seu diretório e rodar um `git fetch` seguido de `git merge origin/master` ou simplesmente um `git pull origin`.
