---
date: 2014-05-31 22:05
layout: post
title: "Cuide bem do seu requirements.txt"
...

Uma das coisas que considero mais úteis, e ao mesmo tempo incrivelmente simples, em ambientes de desenvolvimento modernos é o [gerenciamento de dependências externas][dependencies]. Se for preciso, por exemplo, utilizar uma biblioteca de acesso a API de um serviço externo, como Twitter ou Facebook, você não precisa commitar o código da mesma junto com seu repositório. Nestes casos, basta apenas declará-la no formato do seu gerenciador de dependências, e esta poderá ser facilmente instalável e mantida em qualquer outro ambiente além da sua própria máquina de desenvolvimento, como o computador de outro programador ou o servidor de produção.

Cada linguagem possui uma ou mais ferramentas específicas para isto. No caso do Python, o utilitário [recomendado][recommendations] para esta tarefa é o [pip][pip], que substituiu o por muito tempo utilizado [easy_install][easy_install] (por sua vez parte do [Setuptools][setuptools]). Seu funcionamento é muito simples, bastando utilizar o comando `pip install [pacote]`, de preferência em um [virtualenv][virtualenv], para ter o pacote desejado instalado.

Um recurso muito bacana é a possibilidade de se especificar uma [lista de dependências em um arquivo-texto][requirements] qualquer, normalmente chamado `requirements.txt`, qual pode ser utilizada a partir do comando `pip install -r <arquivo.txt>`. O problema é que, justamente por se tratar de um arquivo muito simples, as pessoas muitas vezes não tem o devido zelo com a manutenção do mesmo - o que é algo que tenho visto, infelizmente, se tornar cada vez mais frequente mesmo entre programadores mais experientes. Por este motivo, compilei esta sucinta lista de boas práticas e cuidados a serem tomados com seus arquivos de dependências externas.

## Nunca gere o `requirements.txt` a partir do comando `pip freeze` sem usar o `grep`

Embora a própria documentação oficial recomende esta prática, por favor não faça isto. O motivo para evitar isso é muito simples: pacotes Python tem informações sobre suas próprias dependências, o que torna desnecessário especificá-las individualmente. Veja o seguinte exemplo, na instalação do [Fabric][fabric]:

    (projetoenv)[myhro@wheezy:~/projeto]$ pip install fabric
    Downloading/unpacking fabric
    [...]
    Successfully installed fabric paramiko pycrypto ecdsa
    Cleaning up...
    (projetoenv)[myhro@wheezy:~/projeto]$ pip freeze > requirements.txt
    (projetoenv)[myhro@wheezy:~/projeto]$ cat requirements.txt
    Fabric==1.8.3
    argparse==1.2.1
    distribute==0.6.24
    ecdsa==0.11
    paramiko==1.12.4
    pycrypto==2.6.1
    wsgiref==0.1.2

Veja que, além das próprias dependências do Fabric, foram adicionados até mesmo os pacotes instalados durante a criação do virtualenv. Em contrapartida, o `requirements.txt` ficaria muito mais simples e legível se criado com o auxílio do comando [grep][grep]:

    (projetoenv)[myhro@wheezy:~/projeto]$ pip freeze | grep -i fabric > requirements.txt
    (projetoenv)[myhro@wheezy:~/projeto]$ cat requirements.txt
    Fabric==1.8.3

A partir daí, novas dependências seriam concatenadas ao final do arquivo com a utilização do redirecionador `>>`:

    (projetoenv)[myhro@wheezy:~/projeto]$ pip install django
    Downloading/unpacking django
    [...]
    Successfully installed django
    Cleaning up...
    (projetoenv)[myhro@wheezy:~/projeto]$ pip freeze | grep -i django >> requirements.txt
    (projetoenv)[myhro@wheezy:~/projeto]$ cat requirements.txt
    Fabric==1.8.3
    Django==1.6.5

Mantê-lo ordenado em ordem alfabética produz um resultado melhor ainda. A busca por uma forma de automatizar esta etapa fica como exercício para o leitor.

## Especifique as versões das bibliotecas

Se você edita o arquivo `requirements.txt` na mão, pode se sentir tentado a adicionar uma entrada apenas com o nome da dependência, da mesma forma qual a instalou na linha de comandos. Embora isto possa parecer interessante, já que você sempre teria acesso à última versão da mesma, isto na verdade pode lhe gerar problemas no futuro. Hoje, o comando `pip install django` instalou o Django 1.6.5, mas e daqui a algum tempo, quando a versão 1.7 for lançada? Se você estiver fazendo uso de classes/funções/módulos quais foram removidos, parte de sua aplicação estará quebrada apenas por inconsistência entre as versões.

## Remova dependências obsoletas

Esta dica é bem óbvia, mas é algo que muitas vezes passa batido: a lista de dependências externas também deve ser atualizada de acordo com as mudanças realizadas na sua base de código, não apenas para adicionar novas entradas, mas também para remover bibliotecas quais não serão mais utilizadas. Embora a economia de espaço em disco não seja muito grande, visto que a maioria das bibliotecas Python são pequenas, a confusão causada por uma dependência qual deveria ter sido removida da lista há meses pode ser.

## Modularize o `requirements.txt` para utilização em diferentes ambientes, quando necessário

Pra que instalar o [IPython][ipython] no servidor de produção? Ou pra que instalar o [Gunicorn][gunicorn] na sua máquina, se localmente você só usa o [servidor de desenvolvimento do Django][django-server]? Como cada linha do arquivo com a lista de dependências é executada em um comando `pip install`, você pode simplesmente adicionar uma entrada `-r <arquivo.txt>`, para instalar as dependências presentes em outra lista.

    (projetoenv)[myhro@wheezy:~/projeto]$ cat development.txt
    -r requirements.txt
    ipython==2.1.0

Desta forma, o comando `pip install -r development.txt` instalaria todas as dependências contidas no arquivo `requirements.txt` e também o IPython.

[dependencies]: http://12factor.net/dependencies
[django-server]: https://docs.djangoproject.com/en/1.6/intro/tutorial01/#the-development-server
[easy_install]: http://pythonhosted.org/setuptools/easy_install.html
[fabric]: http://www.fabfile.org/
[grep]: /2012/01/expressoes-regulares-grep-egrep-fgrep/
[gunicorn]: http://gunicorn.org/
[ipython]: http://ipython.org/
[pip]: http://pip.readthedocs.org/en/latest/
[recommendations]: https://python-packaging-user-guide.readthedocs.org/en/latest/current.html#installation-tool-recommendations
[requirements]: http://pip.readthedocs.org/en/latest/user_guide.html#requirements-files
[setuptools]: http://pythonhosted.org/setuptools/
[virtualenv]: http://virtualenv.readthedocs.org/en/latest/virtualenv.html
