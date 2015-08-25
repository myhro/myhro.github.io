---
date: 2012-01-15 20:17:20
layout: post
title: 'Expressões regulares: grep, egrep, fgrep'
categories:
- er
- linux
---

A ideia deste post não é falar unicamente sobre expressões regulares, também conhecidas como "regex" (do inglês, _regular expressions_), até por ser um assunto extenso com diversos livros dedicados ao tema. Mas como no meu dia a dia percebo que o "grep" é uma das ferramentas que mais utilizo, convém falar um pouquinho dele e seus primos "egrep" e "fgrep". Sendo assim, acaba sendo difícil não citar esta ferramenta incrível que são as expressões regulares. Resumidamente, uma ER é um método formal de se especificar um padrão de texto. A descrição ajuda um pouco, mas é na prática que fica mais fácil de entender sua utilidade.

O [grep](http://www.gnu.org/software/grep/manual/grep.html) é um aplicativo comum em sistemas Unix, que procura pelo texto especificado em um arquivo ou na entrada padrão (caso nenhum arquivo seja utilizado). Como o método mais prático de "casar" textos é utilizando expressões regulares, o grep naturalmente oferece suporte as mesmas. Sua utilização mais simples é com o "fgrep" (que corresponde ao comando "grep -F"), procurando por strings literais (e não ERs). O "egrep" (cujo comando correspondente é o "grep -E") é utilizado para suporte a expressões regulares extendidas, onde não é preciso escapar alguns metacaracteres com a barra invertida (**\\{** vira **{** e **\|** vira **|**, por exemplo).

Na verdade, os comandos "egrep" e "fgrep" são mantidos por razões históricas e seu uso é desencorajado. Desta forma, o "grep" será utilizado nos exemplos, acompanhado das opções "**-E**" ou "**-F**" quando necessário.

Em um arquivo "[.gitignore](http://blog.myhro.info/2011/08/git-para-principiantes/)", podemos ter nomes de arquivos, pastas, máscaras para os mesmos e comentários. E se eu quisesse extrair apenas as máscaras que correspondem as extensões? Com o grep, basta especificar o ***.** (asterisco e ponto):

    myhro@squeeze:~/repositorio$ grep -F '*.' .gitignore
    *.cache
    *.com
    *.config
    *.class
    *.dll
    *.exe
    *.manifest
    *.o
    *.pdb
    *.resources
    *.so
    *.suo
    *.7z
    *.dmg
    *.gz
    *.iso
    *.jar
    *.rar
    *.tar
    *.zip
    *.log
    *.sql
    *.sqlite
    *.tlog
    *.user

É recomendado utilizar o "**-F**" sempre que o termo buscado não for uma expressão regular, até mesmo para se evitar confusões. Além disso, o processo para se buscar uma string literal é mais rápido (embora a diferença só seja notada ao se lidar com grandes quantidades de textos ou executar a busca muitas vezes, como em um loop dentro de um shell script ou em vários arquivos simultaneamente). Você pode capturar apenas as extensões de três letras facilmente:

    myhro@squeeze:~/repositorio$ grep -E '\*\..{3}$' .gitignore
    *.com
    *.dll
    *.exe
    *.pdb
    *.suo
    *.dmg
    *.iso
    *.jar
    *.rar
    *.tar
    *.zip
    *.log
    *.sql

Vamos dissecar a ER '**\*\..{3}$**': as aspas simples são usadas para proteger a expressão e impedir que o interpretador de comandos dê um significado diferente a qualquer um dos caracteres. O **\*** e o **\.** são um asterisco e um ponto, literalmente, por isso são precedidos por uma barra invertida. O **.** (ponto) significa um caractere qualquer, que é quantificado pelo número entre as chaves que o sucede. No caso, procuramos por três caracteres após um asterisco e um ponto, mas isto por si só também abrangeria os casos onde temos extensões com mais de três letras. Para evitar que isto aconteça, dizemos que estes três caracteres são sucedidos por um "fim de linha", representado pelo cifrão.

O contrário do cifrão é o circunflexo, que representa o início de uma linha. No Debian, quando se busca por uma palavra-chave em um nome de pacote, o resultado pode ser uma lista muito grande. Sabendo que um pacote instalado é representado pela letra "i", que tal filtrar apenas os pacotes instalados dessa lista?

    myhro@squeeze:~$ aptitude search gcc | grep '^i'
    i   gcc-4.4-base     - The GNU Compiler Collection (base package)
    i   libgcc1          - GCC support library

Como você pode ver, é muito fácil filtrar a saída de um programa antes de ser exibida na tela com o uso do grep a partir do pipe. Como sabemos identificar o início e o fim de uma linha, podemos descobrir quais são as linhas em branco do arquivo, com o auxílio do parâmetro "**-n**" para numerá-las:

    myhro@squeeze:~/repositorio$ grep -n '^$' .gitignore
    18:
    32:
    41:

Neste caso são poucas linhas e podemos contá-las mentalmente. Utilizando novamente o pipe, é possível enviar a saída do grep para que o [wc](http://www.unix.com/man-page/posix/1posix/wc/) se encarregue da contagem (o que pode ser particularmente útil ao lidar com um número grande de linhas):

    myhro@squeeze:~/repositorio$ grep -n '^$' .gitignore | wc -l
    3

Se quiséssemos encontrar apenas as extensões que começam com vogais, poderíamos fazê-lo facilmente com o auxílio da lista, que é representada pelos colchetes:

    myhro@squeeze:~/repositorio$ grep '\*\.[aeiou]' .gitignore
    *.exe
    *.o
    *.iso
    *.user

Note que a lista significa qualquer um dos caracteres que a compõe e não todos eles simultaneamente. Para buscar pelas consoantes, basta negar a lista com o auxílio do circunflexo:

    myhro@squeeze:~/repositorio$ grep '\*\.[^aeiou]' .gitignore
    *.cache
    *.com
    *.config
    *.class
    *.dll
    *.manifest
    *.pdb
    *.resources
    *.so
    *.suo
    *.7z
    *.dmg
    *.gz
    *.jar
    *.rar
    *.tar
    *.zip
    *.log
    *.sql
    *.sqlite
    *.tlog

É preciso tomar um certo cuidado com a lista negada. Veja que o circunflexo fica dentro da lista (após o primeiro colchete) e não fora dela. Se o circunflexo estivesse por fora (algo como ** ^[aeiou]**), significaria uma linha que começa com qualquer um dos caracteres da lista. Outro detalhe é que neste caso ela significa qualquer coisa que não uma vogal, o que pode ser qualquer outro caractere além de uma consoante, como um ponto ou um espaço. Também é possível criar uma lista utilizando intervalos quaisquer, como **[0-9]** e **[a-z]**.

Por fim, a última dica é com relação a busca com o operador lógico **|** (que significa "OU" (OR)) (não confunda com o pipe utilizado para redirecionar a saída de um comando). Após efetuar uma busca em todo o diretório "/usr/share/doc/", filtramos todos os resultados que terminam em ".html" ou ".txt":

    myhro@squeeze:~$ locate /usr/share/doc | grep -E '\.(html|txt)$'
    (...)
    /usr/share/doc/debian/FAQ/index.html
    /usr/share/doc/discover/guide.html
    /usr/share/doc/exim4-base/README.Debian.html
    /usr/share/doc/gnupg/Upgrading_From_PGP.txt
    /usr/share/doc/gnupg/faq.html
    /usr/share/doc/initramfs-tools/maintainer-notes.html
    /usr/share/doc/joe/help.pl.txt
    /usr/share/doc/kbd/font-formats/font-formats-1.html
    (...)

A utilidade do grep é enorme. É possível utilizá-lo em um "if" após filtrar a saída de um comando a fim de verificar se o resultado é o esperado ou simplesmente extrair o que interessa das mensagens antes que estas cheguem a tela. Para aprender mais sobre expressões regulares e usufruir delas ao máximo, recomendo todo e qualquer material do [Aurelio Jargas](http://aurelio.net/) (sim, [o nome dele não tem acento](http://aurelio.net/blog/2011/05/26/aos-33-descobri-que-meu-nome-nao-tem-acento/)) disponível no [Portal brasileiro de Expressões Regulares](http://aurelio.net/regex/). Em especial, há a apostila "[Conhecendo as Expressões Regulares](http://aurelio.net/regex/apostila-conhecendo-regex.pdf)" e o livro disponível online gratuitamente "[Expressões Regulares - Guia de Consulta Rápida](http://aurelio.net/regex/guia/)".
