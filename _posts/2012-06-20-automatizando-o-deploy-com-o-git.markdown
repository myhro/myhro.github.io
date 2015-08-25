---
date: 2012-06-20 22:31:03
layout: post
title: Automatizando o deploy com o Git
categories:
- deploy
- git
---

Já falei sobre o Git [há algum tempo por aqui](http://blog.myhro.info/2011/08/git-para-principiantes/), mas nunca citei o quão simples é sua utilização como ferramenta de deploy. Todo repositório [Git](http://git-scm.com/) tem uma pasta chamada "hooks", onde estão disponíveis scripts que serão chamados de acordo com determinadas ações. O "post-receive", em especial, é executado assim que um repositório remoto recebe todos os dados de um push. A partir daí, basta escrever um pequeno script que copie estes arquivos para a pasta de produção (como o diretório qual o [Apache](http://httpd.apache.org/) serve um site).

A linguagem utilizada no script fica a critério do freguês, uma vez que o interpretador será definido em sua primeira linha, a famosa [_hashbang_](http://en.wikipedia.org/wiki/Shebang_%28Unix%29) (também conhecida como _shebang_). Como neste caso o script envolve execução como um outro usuário e a moção de arquivos de um lugar para outro, é mais simples fazê-lo em Shell Script. Ele será útil em situações como:

* Seu servidor de produção é o mesmo onde está disponível um gerenciador de repositórios, como o [Gitosis](http://git-scm.com/book/en/Git-on-the-Server-Gitosis).  
* Há apenas um repositório remoto no servidor de produção, utilizado com este mesmo propósito, onde a escrita é possível através de algum protocolo como o SSH.

A única diferença é que, no primeiro caso, você faria diversos "pushes" ao longo do desenvolvimento de um projeto, mas nem sempre gostaria que este código mais recente fosse para a produção. Enquanto, no segundo, você só enviaria o código "pronto" (na verdade o código nunca está pronto - sempre cabe uma melhoria, mesmo que pequena) quando o único destino deste fosse a produção. Descreverei um possível workflow para a primeira opção, mas no fundo vocês verão que a ideia é basicamente a mesma: só vai para produção o que estiver no [branch](http://git-scm.com/book/en/Git-Branching-Basic-Branching-and-Merging) "master".

> **Workflow**
> 
> Um ou mais programadores trabalham no código, enviando os commits relevantes para o servidor. Ele(s) sabe(m) que o branch "master" é sagrado e por isto jamais deve(m) commitar as mudanças diretamente nele. Desta forma, um programador possui diversos branches paralelos, como "bugfix", "testing", "newfeature", etc. Muitas vezes, embora nem sempre, o código destes branches precisa ser compartilhado entre vários programadores. Para isto, basta enviar o código para o servidor com um push, como "git push origin bugfix". Quando o código está maduro, este é mesclado à árvore presente no "master" e o branch onde o mesmo foi criado é apagado. Com o "master" contendo sempre o código estável, é natural que apenas este vá para a produção. Quando o servidor recebe um push com alterações presentes no branch "master", o script de deploy é então executado.

O primeiro script que utilizei para automatizar o deploy com o Git era baseado [neste do Abhijit Menon-Sen](http://toroid.org/ams/git-website-howto), mas a versão atual tem suas ideias oriundas da [versão criada pelo Jon Brisbin](http://jbrisbin.com/web2/articles/rails-application-deployment-with-git/). A diferença, na verdade, reside no fato dele ter sido simplificado para utilizar apenas um único arquivo e também verificar qual branch foi recebido. O script, como foi dito, deve ficar na pasta "hooks" do repositório remoto. Isto é muito importante, pois de nada adianta criar o arquivo em seu repositório local e esperar que ele venha a ser enviado no próximo push.

Antes de adicionar o script ao diretório "hooks", é importante que se edite o arquivo "/etc/sudoers", adicionando a seguinte linha:

``` bash
git ALL=(www-data) NOPASSWD: /bin/tar, /usr/bin/git
```

Para que o usuário "git", normalmente responsável pelos repositórios gerenciados pelo Gitosis, possa usar o "sudo" sem senha e executar os comandos "tar" e "git" como o usuário "www-data".

Como você pode presumir, esta configuração se faz necessária apenas se você estiver realizando o deploy de sites/aplicações web e deseja que os arquivos movidos sejam de propriedade do usuário "www-data". Caso não seja este o caso, basta remover o "sudo" (e seus parâmetros) do script a seguir, assim como adaptar a ação de acordo com suas necessidades (como compilar o projeto, ao invés de apenas mover os arquivos, por exemplo). Vamos ao script em si:

``` bash
#!/bin/sh

DEPLOY="/var/www/"

read OLDREV NEWREV REFNAME
if [ $REFNAME = "refs/heads/master" ]; then
    echo "Starting deploy..."
    sudo -u www-data git archive --format=tar HEAD | sudo -u www-data tar -C $DEPLOY -xf -
    echo "Done."
fi
```

A variável "$DEPLOY" corresponde ao diretório de destino. Como os hashs das revisões, assim como o nome do branch, são recebidos pela entrada padrão (e não passados por parâmetro), é necessário utilizar o comando "read" para armazená-los em variáveis. Em seguida, há apenas uma condição: se o branch recebido via push for o "master", faça o deploy. O comando "git archive" gera um arquivo tar com o conteúdo da working tree, que em seguida é descompactado na pasta especificada no início do script. Os comandos "echo" tem sua saída enviada ao usuário que realizou o push, para que este seja informado se houve ou não o deploy.

Com apenas estas 10 linhas é possível ter um sistema de deploy automatizado extremamente prático para aplicações web. Só se percebe a utilidade do mesmo quando se compara o tempo perdido em sessões de SFTP/SCP (ou ainda, FTP - um protocolo que deveria ter morrido há muitos anos). Além disso, é possível notar o quanto os [hooks do Git](http://git-scm.com/book/en/Customizing-Git-Git-Hooks) (e seus comandos menos conhecidos, como o "[archive](http://www.kernel.org/pub/software/scm/git/docs/v1.7.3/git-archive.html)") podem facilitar nossa vida. Quem disse que sistemas de controle de versão só precisavam armazenar código?
