---
date: 2012-05-21 12:00:03
layout: post
title: Utilizando o pipe
categories:
- linux
- terminal
---

Uma das maiores facilidades presentes em um terminal Linux (ou qualquer sistema Unix-like) é [o pipe](http://workaround.org/linuxtip/pipes), representado pelo caractere '**|**' e dos redirecionadores de entrada e saída, disponíveis através dos símbolos menor '**<**' e maior '**>**'. A grande jogada dos mesmos, que a primeira vista pode não parecer tão extraordinário assim mas é algo extremamente útil, é redirecionar os fluxos [stdin](http://www.cplusplus.com/reference/clibrary/cstdio/stdin/) e [stdout](http://www.cplusplus.com/reference/clibrary/cstdio/stdout/) para arquivos e vice-versa. Falarei aqui primeiramente sobre a utilização do pipe, a partir do exemplo já citado no post "[Expressões regulares: grep, egrep, fgrep](http://blog.myhro.info/2012/01/expressoes-regulares-grep-egrep-fgrep/)", que é bem simples de entender:

    myhro@squeeze:~/repositorio$ grep -n '^$' .gitignore | wc -l
    3

Aqui o grep procuraria pelas linhas em branco, representadas pela expressão regular '**^$**' (que detona um início de linha seguido imediatamente pelo fim de linha), e as escreveria (na verdade sua numeração, devido ao parâmetro "-n") na saída padrão (stdout), que no nosso caso é a tela. O que o pipe faz é, simplesmente, pegar a saída do comando à esquerda e enviar para a entrada padrão (stdin) do comando à direita. O que veríamos na tela foi enviado para o comando [wc](http://en.wikipedia.org/wiki/Wc_(Unix)), que seguido do parâmetro "-l", conta quantas linhas foram lidas e nos mostra este número na tela (ao invés da entrada que foi recebida).

Uma situação de uso muito interessante do pipe é em conjunto com o comando [tee](http://en.wikipedia.org/wiki/Tee_(command)), que basicamente lê algo da entrada padrão e grava em um arquivo e na saída padrão (ao mesmo tempo). Sua descrição por si só parece bem inútil, mas imaginemos a seguinte situação: você quer baixar um arquivo e em seguida calcular seu hash com alguma ferramenta como o [md5sum](https://help.ubuntu.com/community/HowToMD5SUM), de forma a verificar se o mesmo não foi corrompido durante a transferência. A forma mais comum (e ineficiente) e realizar isto é essa (o comando foi separado em várias linhas com o auxílio da barra invertida apenas para facilitar a leitura):

    myhro@squeeze:~$ curl -o git-1.7.txz ftp://ftp.slackware-brasil.com.br/slackware-13.37/slackware/d/git-1.7.4.4-i486-1.txz && md5sum git-1.7.txz
      % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                     Dload  Upload   Total   Spent    Left  Speed
    100 2517k  100 2517k    0     0   126k      0  0:00:19  0:00:19 --:--:--  129k
    5d92bd44e67618dfdacc2e2fa9a41821  git-1.7.txz
    myhro@squeeze:~$ ls
    git-1.7.txz

O [curl](http://curl.haxx.se/) primeiramente baixa o arquivo, que é salvo com a opção "-o" e somente após terminado o download seu hash será calculado com o auxílio do md5sum. O arquivo utilizado como exemplo é pequeno, mas imagine caso o download fosse de uma ISO de DVD, com mais de 4GB. Será se realmente há a necessidade de ler o arquivo duas vezes (primeiro da rede e depois do disco)? Veja o que podemos fazer com o auxílio do tee:

    myhro@squeeze:~$ curl ftp://ftp.slackware-brasil.com.br/slackware-13.37/slackware/d/git-1.7.4.4-i486-1.txz | tee git-1.7.txz | md5sum
      % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                     Dload  Upload   Total   Spent    Left  Speed
    100 2517k  100 2517k    0     0   101k      0  0:00:24  0:00:24 --:--:--  129k
    5d92bd44e67618dfdacc2e2fa9a41821  -
    myhro@squeeze:~$ ls
    git-1.7.txz

Sem o parâmetro "-o" o curl envia o arquivo que está sendo baixado para a saída padrão (com o [wget](http://www.gnu.org/software/wget/) os parâmetros necessários seriam "-O -"). A partir disto fica fácil usar o tee para gravá-lo no disco, mas simultaneamente enviar esta saída para o comando md5sum que estará calculando o hash paralelamente ao download. Ou seja, o arquivo foi lido uma só vez e você obteve seu hash imediatamente, ao invés de esperar alguns preciosos segundos ou até mesmo minutos.
