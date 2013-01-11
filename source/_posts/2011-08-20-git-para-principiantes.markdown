---
date: 2011-08-20 10:37:17
layout: post
slug: git-para-principiantes
title: Git para principiantes
categories:
- git
- programacao
---

No início do próximo mês de setembro, [@Flaudisio](http://twitter.com/Flaudisio), [@HerberthAmaral](http://twitter.com/HerberthAmaral), [@mbodock](http://twitter.com/mbodock) e claro, [eu](http://twitter.com/myhro), realizaremos o [#gitday](http://twitter.com/search/realtime/%23gitday) na Unimontes. O propósito é reunir os interessados, principalmente os calouros que ainda estão no primeiro ano de curso, e apresentar esta maravilhosa ferramenta de controle de versões que é o [Git](http://git-scm.com/). Contando com a colaboração do [Prof. Renato Cota](http://buscatextual.cnpq.br/buscatextual/visualizacv.do?id=K4742948Z3), os trabalhos das disciplinas de Algoritmos e Estruturas de Dados I e II incluirão pontos extras para quem realizá-los versionando-os corretamente com o Git, de forma a incentivar sua utilização desde os primeiros passos no mundo da programação.

Como fiquei responsável por organizar o material básico escrito como introdução, falarei das quatro tarefas fundamentais do Git, que são as seguintes:
	
* Criar um repositório 
* Adicionar novos arquivos e/ou salvar alterações 
* Remover arquivos e/ou reverter alterações 
* Trabalhar com mais de uma linha de desenvolvimento, de forma a reduzir as chances de estragar seu código ao realizar grandes mudanças

Não descreverei a instalação, pois além de ser um processo extramente trivial, está descrito de forma bem clara no guia "[Set Up Git](http://help.github.com/win-set-up-git/)" do disponível na [ajuda do GitHub](http://help.github.com/). Apenas uma única ressalva tem de ser feita sobre este tutorial:

Na parte "Windows Explorer integration" há duas opções: "Context menu entries" e "git-cheetah shell extension" (esta segunda só funciona nas versões 32 bits do Windows). A diferença entre elas reside na forma como você pode acessar o terminal do Git a partir de uma pasta qualquer no Windows Explorer. Com a extensão git-cheetah, você pode entrar em uma pasta qualquer, clicar com o botão direito em uma área vazia (sem ser em cima de algum arquivo) e então encontrar as opções referentes ao Git. Na primeira opção (única disponível nos Windows 64 bits), você tem de clicar com o botão direito na pasta antes de abrí-la para acessar o terminal. Se você o fizer depois de entrar na pasta, as opções simplesmente não aparecem.

Realizei os passos descritos a seguir no Linux pois utilizando o Putty fica mais fácil para copiar/colar os comandos aqui. Embora a aparência seja ligeiramente diferente, os comandos são os mesmos que você utilizará no Windows. Só preste atenção na utilização das letras minúsculas pois os comandos são case-sensitive ("git add" é diferente de "git ADD").

## Criação de um repositório

Preste muita atenção pois este passo é o mais importante e ao mesmo tempo extremamente complicado. Após entrar na pasta qual deseja criar o repositório (seja com a opção "Git Bash Here" no Windows Explorer ou navegando pelas pastas após abrir o "Git Bash" no menu iniciar), digite "git init":

    myhro@squeeze:~/calculadora$ git init
    Initialized empty Git repository in /home/myhro/calculadora/.git/

Pronto. Seu repositório foi inicializado e agora você pode utilizar o Git em sua plenitude. Foi criada uma subpasta oculta chamada ".git" dentro da pasta qual você está no momento em que digitou o comando, com alguns arquivos e outras subpastas. Você não precisa se preocupar com eles, pois provavelmente nunca será necessário modificar nada nestes arquivos diretamente. Tudo é feito utilizando o comando "git" seguido dos seus respectivos parâmetros.

## Adicionando arquivos e "commitando" alterações

Mesmo que haja arquivos na pasta ou você crie alguns depois de ter inicializado o repositório, este continua vazio. O Git não se preocupa com arquivos que não foram adicionados e isto você tem de fazer manualmente. Os arquivos que se encontram na pasta (ou mesmo em subpastas) do repositório podem estar em dois estados: tracked or untracked. "Tracked" significa que o arquivo faz parte do repositório e "untracked" o contrário. Para verificar o estado atual do seu repositório, digite "git status":

    myhro@squeeze:~/calculadora$ git status
    # On branch master
    #
    # Initial commit
    #
    # Untracked files:
    #   (use "git add <file>..." to include in what will be committed)
    #
    #       calc.c
    #       calc.exe
    nothing added to commit but untracked files present (use "git add" to track)

A saída deste comando por si só é bem explicativa. Há dois arquivos (um é o código-fonte e o outro é o executável) na pasta mas nenhum foi adicionado ao repositório. Para fazer isto basta digitar "git add arquivo" (o que não vai gerar nenhuma saída na tela) e em seguida verificar novamente o estado do repositório:

    myhro@squeeze:~/calculadora$ git add calc.c
    myhro@squeeze:~/calculadora$ git status
    # On branch master
    #
    # Initial commit
    #
    # Changes to be committed:
    #   (use "git rm --cached <file>..." to unstage)
    #
    #       new file:   calc.c
    #
    # Untracked files:
    #   (use "git add <file>..." to include in what will be committed)
    #
    #       calc.exe

Como prefiro evitar que arquivos binários sejam adicionados, criarei um arquivo chamado ".gitignore" (um arquivo é muito útil, servindo principalmente para impedir a adição arquivos desnecessários ou confidenciais ao repositório acidentalmente) contendo uma única linha: "*.exe" (sem aspas) e o adicionarei da mesma forma:

    myhro@squeeze:~/calculadora$ git add .gitignore
    myhro@squeeze:~/calculadora$ git status
    # On branch master
    #
    # Initial commit
    #
    # Changes to be committed:
    #   (use "git rm --cached <file>..." to unstage)
    #
    #       new file:   .gitignore
    #       new file:   calc.c

Agora os arquivos estão prontos para serem "commitados". Um commit nada mais é do que gravar suas ações atuais no repositório. A sintaxe do comando "commit" é "git commit -m 'descrição das mudanças realizadas'", podendo ser utilizado em conjunto com o parâmetro "-a" (como "git commit -am 'mensagem'"). O "-m" é responsável por guardar a descrição do commit, o que é muito útil quando se está analisando o log posteriormente (por favor, nunca "commite" nada sem uma descrição apropriada). O "-a" serve para não se ter de utilizar o "git add" novamente quando você modificar arquivos que já estão no repositório. O Git faz o possível para não gravar alterações que você não queira, portanto é sempre necessário adicionar os arquivos manualmente, sejam estes novos ou modificados. O "-a" serve para agilizar as coisas quando estamos apenas realizando modificações e não adições.

    myhro@squeeze:~/calculadora$ git commit -m "Primeiro commit"
    [master (root-commit) 083f21f] Primeiro commit
     2 files changed, 6 insertions(+), 0 deletions(-)
     create mode 100644 .gitignore
     create mode 100644 calc.c
    myhro@squeeze:~/calculadora$ git status
    # On branch master
    nothing to commit (working directory clean)

Após realizar algumas alterações no arquivo, você pode comparar a versão atual com a última versão "commitada" com o comando "git diff" (o "**+**" representa uma linha adicionada e o "**-**" uma removida. Normalmente a saída é colorida em verde e vermelho, respectivamente, por padrão para facilitar a visualização). Em seguida, basta "commitar" novamente utilizando o parâmetro "-a" para salvar as modificações no repositório:

    myhro@squeeze:~/calculadora$ git status
    # On branch master
    # Changed but not updated:
    #   (use "git add <file>..." to update what will be committed)
    #   (use "git checkout -- <file>..." to discard changes in working directory)
    #
    #       modified:   calc.c
    no changes added to commit (use "git add" and/or "git commit -a")
    myhro@squeeze:~/calculadora$ git diff
    diff --git a/calc.c b/calc.c
    index 6176a11..bf4095f 100644
    --- a/calc.c
    +++ b/calc.c
    @@ -1,5 +1,9 @@
     #include <stdio.h>
    
     int main(int argc, char *argv[]) {
    +    if (argc < 4) {
    +        printf("Faltam argumentos para realizar a operacao.\n");
    +        return 1;
    +    }
         return 0;
     }
    myhro@squeeze:~/calculadora$ git commit -am "Verificacao do numero de argumentos"
    [master bd96868] Verificacao do numero de argumentos
     1 files changed, 4 insertions(+), 0 deletions(-)
    myhro@squeeze:~/calculadora$ git status
    # On branch master
    nothing to commit (working directory clean)

## Revertendo modificações

Nenhum sistema de controle de versões está completo se for possível apenas realizar modificações sem poder reverter os arquivos para um ponto qualquer no tempo. O conceito de versionamento é justamente este: todas as versões dos arquivos que você "commitou" estão disponíveis integralmente. Você pode reverter um arquivo para exatamente o que ele era há dias ou semanas atrás. Não importa se você adicionou, modificou ou removeu seu conteúdo depois disso.

Seu uso mais comum é justamente descartar alterações em um arquivo desde o último commit. Sabe quando você realizou algumas modificações, mas não se lembra mais exatamente quantas ou quais foram e fica inseguro ao apertar o "CTRL+Z" repetidamente? O comando "checkout" serve justamente pra isso (embora também tenha outra função, ao se trabalhar com branches).

    myhro@squeeze:~/calculadora$ git status
    # On branch master
    # Changed but not updated:
    #   (use "git add <file>..." to update what will be committed)
    #   (use "git checkout -- <file>..." to discard changes in working directory)
    #
    #       modified:   calc.c
    #
    no changes added to commit (use "git add" and/or "git commit -a")
    myhro@squeeze:~/calculadora$ git diff
    diff --git a/calc.c b/calc.c
    index bf4095f..f930da7 100644
    --- a/calc.c
    +++ b/calc.c
    @@ -1,3 +1,4 @@
    +#include <stdlib.h>
     #include <stdio.h>
    
     int main(int argc, char *argv[]) {
    @@ -5,5 +6,6 @@ int main(int argc, char *argv[]) {
             printf("Faltam argumentos para realizar a operacao.\n");
             return 1;
         }
    +    printf("Operacao: %d %s %d\n", atoi(argv[1]), argv[2], atoi(argv[3]));
         return 0;
     }
    myhro@squeeze:~/calculadora$ git checkout -- calc.c
    myhro@squeeze:~/calculadora$ git diff
    myhro@squeeze:~/calculadora$ git status
    # On branch master
    nothing to commit (working directory clean)

Se você tivesse apagado o arquivo e utilizasse o "git checkout", o que aconteceria? Ele reverteria sua última modificação, que foi justamente a remoção de um arquivo. Desta forma você pode recuperar um arquivo que sequer estava na lixeira de uma maneira muito simples e rápida.

Para reverter um arquivo para uma versão específica do repositório, basta consultar o log do Git com o comando "git log", encontrar o commit desejado na lista e revertê-lo com base no seu identificador único. Este hash pode parecer assustador, mas você não precisa utilizá-lo em sua totalidade pois bastam apenas os primeiros dígitos.

    myhro@squeeze:~/calculadora$ git log
    commit 989a8e90b89a4b7072480757d14c40887187295a
    Author: Tiago Ilieve <ilieve@email.com>
    Date:   Sat Aug 20 00:43:05 2011 -0300
    
        Imprime a operacao a ser realizada
    
    commit bd96868b883d0f7374be9b22ab7b113edf312753
    Author: Tiago Ilieve <ilieve@email.com>
    Date:   Sat Aug 20 00:12:48 2011 -0300
    
        Verificacao do numero de argumentos
    
    commit 083f21fdb866a15da0a285d5e847aa32e499980a
    Author: Tiago Ilieve <ilieve@email.com>
    Date:   Sat Aug 20 00:04:52 2011 -0300
    
        Primeiro commit
    myhro@squeeze:~/calculadora$ git status
    # On branch teste
    nothing to commit (working directory clean)
    myhro@squeeze:~/calculadora$ git diff bd96
    diff --git a/calc.c b/calc.c
    index bf4095f..f930da7 100644
    --- a/calc.c
    +++ b/calc.c
    @@ -1,3 +1,4 @@
    +#include <stdlib.h>
     #include <stdio.h>
    
     int main(int argc, char *argv[]) {
    @@ -5,5 +6,6 @@ int main(int argc, char *argv[]) {
             printf("Faltam argumentos para realizar a operacao.\n");
             return 1;
         }
    +    printf("Operacao: %d %s %d\n", atoi(argv[1]), argv[2], atoi(argv[3]));
         return 0;
     }
    myhro@squeeze:~/calculadora$ git checkout bd96 calc.c
    myhro@squeeze:~/calculadora$ git diff bd96
    myhro@squeeze:~/calculadora$ git status
    # On branch teste
    # Changes to be committed:
    #   (use "git reset HEAD <file>..." to unstage)
    #
    #       modified:   calc.c

Você pode não apenas reverter as alterações, como remover completamente um arquivo do disco mantendo suas versões antigas gravadas. Há duas formas de fazer isto: deletando o arquivo sem utilizar o git, como você faria normalmente e depois "commitando" com a opção "-a" ou utilizando o comando "git rm".

    myhro@squeeze:~/calculadora$ git add funcoes.h
    myhro@squeeze:~/calculadora$ git commit -m "Header funcoes.h adicionado"
    [master 0f9a1e4] Header funcoes.h adicionado
     1 files changed, 12 insertions(+), 0 deletions(-)
     create mode 100644 funcoes.h
    myhro@squeeze:~/calculadora$ rm funcoes.h
    myhro@squeeze:~/calculadora$ git status
    # On branch master
    # Changed but not updated:
    #   (use "git add/rm <file>..." to update what will be committed)
    #   (use "git checkout -- <file>..." to discard changes in working directory)
    #
    #       deleted:    funcoes.h
    no changes added to commit (use "git add" and/or "git commit -a")
    myhro@squeeze:~/calculadora$ git commit -am "Header funcoes.h removido"
    [master ae0a92b] Header funcoes.h removido
     1 files changed, 0 insertions(+), 12 deletions(-)
     delete mode 100644 funcoes.h

O comando "git rm" possibilita a mesma operação sem utilizar o "-a" no commit:

    myhro@squeeze:~/calculadora$ git rm funcoes.h
    rm 'funcoes.h'
    myhro@squeeze:~/calculadora$ git status
    # On branch master
    # Changes to be committed:
    #   (use "git reset HEAD <file>..." to unstage)
    #
    #       deleted:    funcoes.h
    myhro@squeeze:~/calculadora$ git commit -m "Header funcoes.h removido"
    [master a4d15ea] Header funcoes.h removido
     1 files changed, 0 insertions(+), 12 deletions(-)
     delete mode 100644 funcoes.h

Notou a diferença na saída do comando "git status"? A explicação é a seguinte: no diretório de trabalho do Git, quando um arquivo faz parte do repositório (ou seja, está "tracked") ele possui três estados possíveis: unmodified, modified ou staged. Se ele está marcado como "unmodified" significa que não sofreu nenhuma alteração desde o último commit e sequer aparece na saída do comando "git status". Se o seu estado é "modified", isto aconteceu pelo fato do arquivo ter sofrido alteração mas ainda não foi adicionado à lista de arquivos que serão "commitados" (por isso a necessidade do "git add" ou "git commit -a"). E por último, se ele foi adicionado ou removido ("git add" ou "git rm") à lista do do próximo commit, seu estado é "staged".

A partir disto, você pode não apenas remover o arquivo do seu disco rígido, como removê-lo da lista de arquivos a serem "commitados":

    myhro@squeeze:~/calculadora$ git add outro.c
    myhro@squeeze:~/calculadora$ git status
    # On branch master
    # Changes to be committed:
    #   (use "git reset HEAD <file>..." to unstage)
    #
    #       new file:   outro.c
    myhro@squeeze:~/calculadora$ git reset HEAD outro.c
    myhro@squeeze:~/calculadora$ git status
    # On branch master
    # Untracked files:
    #   (use "git add <file>..." to include in what will be committed)
    #
    #       outro.c
    nothing added to commit but untracked files present (use "git add" to track)

Você já deve ter percebido que o comando "git status" é uma verdadeira mão na roda, principalmente quando há duvidas se o comando a ser utilizado é um "git checkout" ou "git reset".

## Lidando com branches: como trabalhar com linhas de desenvolvimento completamente independentes

Se você quisesse fazer uma mudança realmente profunda em seu código, seja reverter para uma versão de muito tempo atrás ou modificar determinadas funções cruciais que poderiam inutilizar seu trabalho de dias, o que você faria? O mais sensato seria no mínimo criar uma cópia, com CTRL+C/CTRL+V dos seus arquivos caso aconteça uma catástrofe. Com o Git você não precisa disto. Para este tipo de situação existe o conceito de "branch", que ficará muito claro no próximo exemplo. O básico é criar um novo branch com o comando "git branch nome_do_branch" e mudar para ele com o "git checkout nome_do_branch". O parâmetro "-b" poderia ser utilizado com o "git checkout" para simplificar o processo criando e mudando para o novo branch imediatamente. Mesmo assim, prefiro criar o branch separadamente e depois utilizar o comando "git checkout" para navegar livremente entre os mesmos.

    myhro@squeeze:~/calculadora$ git log
    commit a4d15ea9651e09648131e78a95a625a31b0ddc87
    Author: Tiago Ilieve <ilieve@email.com>
    Date:   Sat Aug 20 01:00:39 2011 -0300
    
        Header funcoes.h removido
    
    commit 0f9a1e4e8d23410f11c465edbfb67603c8138c19
    Author: Tiago Ilieve <ilieve@email.com>
    Date:   Sat Aug 20 00:53:31 2011 -0300
    
        Header funcoes.h adicionado
    
    commit 989a8e90b89a4b7072480757d14c40887187295a
    Author: Tiago Ilieve <ilieve@email.com>
    Date:   Sat Aug 20 00:43:05 2011 -0300
    
        Imprime a operacao a ser realizada
    myhro@squeeze:~/calculadora$ git branch
    * master
    myhro@squeeze:~/calculadora$ git branch temporario
    myhro@squeeze:~/calculadora$ git branch
    * master
      temporario
    myhro@squeeze:~/calculadora$ git checkout temporario
    Switched to branch 'temporario'
    myhro@squeeze:~/calculadora$ git branch
      master
    * temporario

A partir deste momento, toda e qualquer modificação que for realizada no repositório não afetará a linha de desenvolvimento principal, carinhosamente chamada de "master" por padrão:

    myhro@squeeze:~/calculadora$ git reset --hard 083f
    HEAD is now at 083f21f Primeiro commit
    myhro@squeeze:~/calculadora$ git log
    commit 083f21fdb866a15da0a285d5e847aa32e499980a
    Author: Tiago Ilieve <ilieve@email.com>
    Date:   Sat Aug 20 00:04:52 2011 -0300
    
        Primeiro commit

Voltando para o branch "master" e verificando o log percebe-se que nada aqui foi modificado:

    myhro@squeeze:~/calculadora$ git checkout master
    Switched to branch 'master'
    myhro@squeeze:~/calculadora$ git log
    commit a4d15ea9651e09648131e78a95a625a31b0ddc87
    Author: Tiago Ilieve <ilieve@email.com>
    Date:   Sat Aug 20 01:00:39 2011 -0300
    
        Header funcoes.h removido
    
    commit 0f9a1e4e8d23410f11c465edbfb67603c8138c19
    Author: Tiago Ilieve <ilieve@email.com>
    Date:   Sat Aug 20 00:53:31 2011 -0300
    
        Header funcoes.h adicionado
    
    commit 989a8e90b89a4b7072480757d14c40887187295a
    Author: Tiago Ilieve <ilieve@email.com>
    Date:   Sat Aug 20 00:43:05 2011 -0300
    
        Imprime a operacao a ser realizada

Melhor ainda: e se caso eu tivesse adicionado algumas funções no branch secundário e quisesse integrá-las ao "master"?

    myhro@squeeze:~/calculadora$ git branch teste
    myhro@squeeze:~/calculadora$ git checkout teste
    Switched to branch 'teste'
    myhro@squeeze:~/calculadora$ git diff
    diff --git a/calc.c b/calc.c
    index f930da7..d341478 100644
    --- a/calc.c
    +++ b/calc.c
    @@ -2,10 +2,23 @@
     #include <stdlib.h>
     #include <stdio.h>
    
     int main(int argc, char *argv[]) {
    +    char op;
    +    int a, b, c;
         if (argc < 4) {
             printf("Faltam argumentos para realizar a operacao.\n");
             return 1;
         }
    -    printf("Operacao: %d %s %d\n", atoi(argv[1]), argv[2], atoi(argv[3]));
    +    op = argv[2][0];
    +    a = atoi(argv[1]);
    +    b = atoi(argv[3]);
    +    if (op == '+') c = a + b;
    +    else if (op == '-') c = a - b;
    +    else if (op == '*') c = a * b;
    +    else if (op == '/' && b != 0) c = a / b;
    +    else {
    +        printf("Nao foi possivel realizar a operacao.\n");
    +        return 1;
    +    }
    +    printf("Operacao: %d %c %d = %d\n", a, op, b, c);
         return 0;
     }
    myhro@squeeze:~/calculadora$ git commit -am "Soma, subtracao, multiplicacao e divisao"
    [teste 959fa0d] Soma, subtracao, multiplicacao e divisao
     1 files changed, 14 insertions(+), 1 deletions(-)
    myhro@squeeze:~/calculadora$ git log
    commit 959fa0d1f3a7d89abed46ccf073f34de4cf46576
    Author: Tiago Ilieve <ilieve@email.com>
    Date:   Sat Aug 20 01:48:14 2011 -0300
    
        Soma, subtracao, multiplicacao e divisao

Bastaria realizar as modificações e testes necessários, retornar para o branch "master" e mesclar o código com apenas um comando (o "git branch -d" apaga o branch após o "git merge"):

    myhro@squeeze:~/calculadora$ git checkout master
    Switched to branch 'master'
    myhro@squeeze:~/calculadora$ git log
    commit a4d15ea9651e09648131e78a95a625a31b0ddc87
    Author: Tiago Ilieve <ilieve@email.com>
    Date:   Sat Aug 20 01:00:39 2011 -0300
    
        Header funcoes.h removido
    myhro@squeeze:~/calculadora$ git merge teste
    Updating a4d15ea..959fa0d
    Fast-forward
     calc.c |   15 ++++++++++++++-
     1 files changed, 14 insertions(+), 1 deletions(-)
    myhro@squeeze:~/calculadora$ git log
    commit 959fa0d1f3a7d89abed46ccf073f34de4cf46576
    Author: Tiago Ilieve <ilieve@email.com>
    Date:   Sat Aug 20 01:48:14 2011 -0300
    
        Soma, subtracao, multiplicacao e divisao
    myhro@squeeze:~/calculadora$ git branch -d teste
    Deleted branch teste (was 959fa0d).

Este é o básico do Git. Ainda há mais coisas como repositórios remotos e merges complexos envolvendo árvores de desenvolvimento ao invés de linhas, mas para controlar e melhorar o gerenciamento do seu código as dicas aqui citadas já são suficientes. Há muito material disponível gratuitamente na internet, incluindo livros como o "[Pro Git](http://progit.org/book/)" e o "[Git Community Book](http://book.git-scm.com/)". O site "[Git Reference](http://gitref.org/)" tem uma série de exemplos que ilustram a utilização corriqueira do Git (e também foi feito pelo pessoal do [GitHub](https://github.com/)). Todos os créditos pelo Git vão para o seu criador, Linus Torvalds, e para quem mais contribuiu com sua simplificação para que pudesse ser utilizado por "pessoas normais", Junio Hamano. No Youtube está disponível uma [palestra do Linus Torvalds no Google Tech Talk](http://www.youtube.com/watch?v=4XpnKHJAok8) em 2007 inteiramente sobre o Git.

Quem tiver se interessado por esta obra-prima em forma de software, recomendo que procurem conhecer mais ainda seu funcionamento e se acostumar com a sintaxe dos seus comandos. Espero vê-los no [#gitday](http://twitter.com/search/realtime/%23gitday) no mês que vem.
