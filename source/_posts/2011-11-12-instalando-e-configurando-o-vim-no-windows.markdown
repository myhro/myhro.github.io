---
date: 2011-11-12 10:19:25
layout: post
slug: instalando-e-configurando-o-vim-no-windows
title: Instalando e configurando o Vim no Windows
categories:
- vim
- windows
---

Após passar alguns dias procurando um bom tutorial de instalação do [Vim](http://en.wikipedia.org/wiki/Vim_(text_editor)) (de configuração na verdade, já que [instalar não é nem de longe a parte mais difícil](http://www.thegeekstuff.com/2009/12/vim-editor-for-windows/)) e não encontrar, resolvi colocar a mão na massa. Após algumas horas (em torno de seis, na verdade), consegui deixar este maravilhoso editor do jeito que queria. Ou pelo menos algo bem próximo disso. Ficou faltando um auto-completar mais abrangente, com as funções nativas da linguagem qual se está programando (e não apenas as quais escrevo). [Não é impossível fazê-lo](http://vim.wikia.com/wiki/C%2B%2B_code_completion), mas achei um tanto quanto desnecessário ter de realizar um [procedimento diferente](http://www.mohdshakir.net/2007/12/27/enable-vim-code-python-auto-complete) para cada linguagem qual se pretenda utilizar.

O [Vim](http://www.vim.org/) é um editor de textos mítico. Lembro que fiz de tudo para evitar qualquer contato com o mesmo (ou seu antecessor, o [vi](http://en.wikipedia.org/wiki/Vi)) nos meus primeiros anos de aventura no mundo Linux. [Seus comandos](http://www.tuxfiles.org/linuxhelp/vimcheat.html) "sem pé nem cabeça" (ao menos era o que eu pensava na época - hoje vejo a grande jogada que é evitar ao máximo o movimento das mãos sobre o teclado) me assustavam só de ler a respeito. O primeiro contato com o Vim normalmente não é muito diferente [deste relato do Aurélio Jargas](http://aurelio.net/blog/2009/06/30/10anos-vim/). Tirinhas como [esta](http://vidadeprogramador.com.br/2011/04/11/strings-aleatorias/), do [Vida de Programador](http://vidadeprogramador.com.br/), refletem perfeitamente o desespero que bate ao se tentar fechar o editor. Se é assim para sair, imagine o trabalho que não deve se ter para usá-lo?

A verdade é que embora o Vim seja assustador em um primeiro contato, em poucos minutos é possível aprender seus comandos básicos e começar a amansar a fera. A partir do "[Vim tutor](http://vimdoc.sourceforge.net/htmldoc/usr_01.html#tutor)" (se você estiver usando o Linux pode acessá-lo com o comando "vimtutor"; no Windows há um atalho no menu iniciar), você pode seguir as 7 lições básicas e começar a se acostumar com os comandos mais comuns. Este guia é simplesmente um arquivo texto aberto com o próprio Vim. Você pode lê-lo, estudá-lo e praticá-lo quantas vezes sentir necessidade. Para relembrar rapidamente um comando esquecido, basta consultar a tabela "[vi/vim graphical cheat sheet](http://www.viemu.com/vi-vim-cheat-sheet.gif)". A curva de aprendizado é acentuada, mas a recompensa é inigualável.

Depois de realizada a instalação (qual sugiro que seja desmarcada a opção "_Add an Edit-with-Vim context menu entry_" - logo mais você entenderá porque), crie um arquivo chamado "\_vimrc" no seu diretório pessoal ("C:\Documents and Settings\Usuario" no Windows XP ou "C:\Users\Usuario" no Windows Vista/7). Este arquivo é responsável pelas configurações que tornam o uso do Vim mais agradável e é completamente personalizável de acordo com o gosto do freguês.

O meu "\_vimrc" ficou assim:

    " Desabilitar o mouse:
    set mouse=
    
    " Desabilitar menus:
    set guioptions-=m
    set guioptions-=r
    set guioptions-=T
    
    " Desabilitar backups:
    set nobackup
    set noswapfile
    set nowritebackup
    
    " Cores e tema:
    colorscheme slate
    filetype on
    syntax on
    
    " Clipboard do sistema:
    set clipboard=unnamed
    
    " Tabs por espaços:
    set expandtab
    set shiftwidth=4
    set tabstop=4
    
    " Indentação:
    filetype plugin indent on
    set autoindent
    
    " Régua, quebra e número de linhas:
    set linebreak
    set number
    set ruler
    
    " Navegação entre abas:
    map <C-Tab> :tabnext<CR>
    map <S-Tab> :tabprevious<CR>
    
    " Busca:
    set hlsearch
    set ignorecase
    set incsearch
    " Limpar os resultados destacados:
    nmap <silent> <C-C> :silent noh<CR>
    
    " Fonte e janela:
    set guifont=consolas:h10
    set encoding=utf-8
    set lines=36 columns=142
    set wildmenu
    " Salvar:
    nmap <silent> <C-S> :silent w<CR>

Os parâmetros estão separados por seções. Sendo assim, vou explicar o propósito de cada uma:
	
* Desabilitar o mouse: se você usa o mouse no Vim, _you are doing it wrong_!  
* Desabilitar menus: a toolbar do gVim (modo gráfico do Vim, usado por padrão no Windows) ocupa espaço, torna a aparência do mesmo esquisita (ele foi feito para ser um editor "modo texto") e induz o usuário a utilizar o mouse, que é justamente a última coisa que você vai precisar ao lidar com o Vim.  
* Desabilitar backups: evita que o Vim crie arquivos começando com "~" ou terminados em ".swp" desnecessariamente.  
* Cores e tema: habilita o syntax hightlighting e escolhe um tema para fugir do padrão "preto no branco", que é o default. Gostei bastante do "slate". É um tema escuro e lembra vagamente o "Obisidian", qual utilizo no [Notepad++](http://notepad-plus-plus.org/).  
* Clipboard do sistema: força o Vim a utilizar a área de transferência padrão do sistema ao invés dos seus próprios clipboards. Assim você pode copiar "de" e "para" ele sem qualquer preocupação.  
* Tabs por espaços: troca o tamanho padrão das tabulações de 8 para 4 espaços (utilizando quatro espaços - literalmente - ao invés de um "\t").  
* Indentação: como o próprio nome já diz, habilita a indentação automática dos blocos de texto.  
* Régua, quebra e número de linhas: mostra o tempo todo na barra de status a linha e coluna atual, além de mostrar a contagem das linhas e quebrá-las por palavras (ao invés de caracteres).  
* Navegação entre abas: possibilita a navegação entre as abas utilizando CTRL+TAB/SHIFT+TAB.  
* Busca: habilita o destaque visual das palavras quais casam com a busca realizada, além de torná-la case-insensitive. A opção "Limpar os resultados destacados" serve para mapear o comando CTRL+C (que não tem utilidade por padrão) para remover o efeito "marca texto" causado pela busca. Caso contrário as palavras continuariam marcadas indefinidamente.  
* Fonte e janela: troca a fonte padrão (no Windows é a Fixedsys - não que esta seja ruim - que tem os traços muito grossos) pela Consolas, seta a codificação padrão como UTF-8 e ajusta o tamanho da janela para ficar próximo de 1024x600 pixels. É uma opção pessoal, pois não gosto de editores de texto maximizados ocupando a tela toda do meu notebook (que tem resolução de 1333x768 px). O wildmenu ajuda na navegação por arquivos no menu ":" e a opção "Salvar" faz com que o atalho CTRL+S salve o arquivo (o mesmo que digitar ESC+":w" (sem aspas)).

Ao final, a aparência do seu editor deve estar mais ou menos assim:


{% img center /images/2011/vim-slate.png %}

Como havia sugerido, desmarcando a opção de chamar o Vim pelo menu de contexto, você acaba perdendo o atalho extremamente prático de editar qualquer arquivo com um clique do botão direito. O problema é que a extensão do shell que acompanha o Vim para Windows não cria abas ao se editar vários arquivos. Felizmente isto pode ser facilmente resolvido com uma simples adição no registro do Windows. Basta salvar um arquivo ".reg" com o seguinte conteúdo (ou editar manualmente o registro) e executá-lo em seguida (o comando é uma linha só do "@" até as últimas aspas):

    REGEDIT4
    [HKEY_CLASSES_ROOT\*\Shell\Edit with &Vim\command]
    @="\"C:\\Program Files (x86)\\Vim\\vim73\\gvim.exe\"
    -p --remote-tab-silent \"%1\" \"%*\""

Para ter acesso ao recuso "auto-complete", que como disse, se não configurá-lo para cada linguagem ele não vai ser perfeito (mesmo assim quebra um galho), basta baixar o plugin [SuperTab](http://www.vim.org/scripts/script.php?script_id=1643) e colocar o [arquivo ".vim"](https://github.com/ervandew/supertab/blob/master/plugin/supertab.vim) na pasta "C:\Program Files (x86)\Vim\vim73\plugin". Daí em diante o Vim passará a carregar o plugin automaticamente e você pode completar os nomes das funções utilizando a tecla "Tab". Só preste atenção no nome da pasta onde o Vim está instalado, pois pode não ser o mesmo se a versão do seu Windows não for 64 bits.

Daí em diante, basta encarar diariamente este editor tão peculiar, interessante e único que é o Vim. Tendo [completado 20 anos no início deste mês](http://arstechnica.com/open-source/news/2011/11/two-decades-of-productivity-vims-20th-anniversary.ars), existem pessoas que [o utilizam há décadas e ainda aprendem coisas novas](http://stackoverflow.com/questions/597077/is-learning-vim-worth-the-effort) no dia-a-dia. Você provavelmente não vai dominá-lo na primeira vez que o utilizar (e talvez este dia nunca chegue), mas pode ter a certeza que nenhum minuto gasto atrás dessa "janelinha preta", como diria o [Carlão](http://twitter.com/carlosmr12), foi desperdiçado. Cada fração de segundo dedicada ao Vim resultam em muitas horas ganhas em produtividade.

**Update**: os plugins e configurações que utilizo no Vim agora [estão disponíveis no GitHub](https://github.com/myhro/vimfiles).

Referências:  
1. [Accessing the system clipboard](http://vim.wikia.com/wiki/Accessing_the_system_clipboard)  
2. [carllerche / vimrc](https://github.com/carllerche/vimrc)  
3. [Consolas in GVIM on Windows](http://jrwren.wrenfam.com/blog/2006/10/31/consolas-in-gvim-on-windows/)  
4. [Converting tabs to spaces](http://vim.wikia.com/wiki/Converting_tabs_to_spaces)  
5. [Getting cool auto-indent in vim](http://blogs.gnome.org/johannes/2006/11/10/getting-cool-auto-indent-in-vim/)  
6. [Hide toolbar or menus to see more text](http://vim.wikia.com/wiki/Hide_toolbar_or_menus_to_see_more_text)  
7. [How did I live without vim's wildmenu all these years?](http://www.dickscheid.net/2011/04/17-vimsWildmenu/)  
8. [Quick VIM question (unhighlighting search terms after search)](http://www.linuxquestions.org/questions/linux-newbie-8/quick-vim-question-unhighlighting-search-terms-after-search-179177/)  
9. [Vim 7 + Putty + Copy Paste](http://eligere.wordpress.com/2008/04/20/vim-7-putty-copy-paste/)  
10. [Vim Recipes: Inserting Accented or "Foreign" Characters](http://vim.runpaint.org/typing/inserting-accented-characters/)  
11. [VIM Swap and backup files](http://thehumblecoder.wordpress.com/2006/08/08/vim-swap-and-backup-files/)  
