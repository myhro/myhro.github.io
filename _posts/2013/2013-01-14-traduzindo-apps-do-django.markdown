---
date: 2013-01-14 13:40
layout: post
title: "Traduzindo apps do Django"
...

No final do ano passado, depois de mais de um ano, decidi por encerrar meu estágio na [Aptans](http://aptans.com/). Foi um período muito bacana da minha carreira como [SysAdmin](http://xkcd.com/705/) (eu realmente gosto muito de trabalhar com Linux), mas optei por sair de lá para adquirir experiência como programador. Após alguns poucos meses apenas estudando e auxiliando os calouros de Engenharia Civil na monitoria da disciplina de [Introdução à Computação](http://www.cpdee.ufmg.br/~rdmaia/IntroComp.htm), passei a trabalhar na [MinasSoft](http://www.minascurriculos.com.br/) como desenvolvedor [Python](http://www.python.org/)/[Django](https://www.djangoproject.com/).

Em um dos sistemas quais estamos desenvolvendo, surgiu a necessidade de utilizarmos o e-mail para autenticação, acompanhado da senha, no lugar de se criar um nome de usuário. Seria algo trivial, já que além da simplicidade da funcionalidade, o Django é conhecido por vir com "[baterias inclusas](https://docs.djangoproject.com/en/1.4/#other-batteries-included)", contendo bibliotecas prontas para quase tudo que você possa precisar. O problema é que devido a [algumas limitações no seu sistema de autenticação](http://stackoverflow.com/questions/3547024/email-as-username-in-django), como o campo do usuário ser limitado a 30 caracteres (o que pode ser muito pouco para um endereço de e-mail), o processo [acaba não sendo tão simples](http://soyeahdjango.com/post/39983945126/django-devs-trying-to-use-email-addresses-as-the) de ser realizado.

A solução mais simples encontrada foi utilizar a app "[Django Email as Username](https://github.com/dabapps/django-email-as-username)", que nada mais é do que uma fina camada de abstração em cima das classes que compõe o sistema de autenticação do Django, tornado o processo completamente indolor. Uma das coisas que ela faz é gerar um [hash](https://en.wikipedia.org/wiki/Cryptographic_hash_function) do email, em uma mistura de [SHA-256 ](https://en.wikipedia.org/wiki/SHA-2)com [Base64](https://en.wikipedia.org/wiki/Base64), e cortar os primeiros 30 caracteres para utilizar como nome de usuário. Até aí tudo bem, se não fosse um detalhe: as mensagens de erro desta aplicação não estão traduzidas.

Como é um tanto quanto complicado entregar um sistema completamente em português com mensagens esporádicas aparecendo em inglês, saí em busca de uma forma de traduzí-las, sem necessariamente alterar diretamente o código-fonte da biblioteca - o que funcionaria, mas não é a forma correta de fazê-lo (pense nas implicações que isso traria quando houvessem modificações no código original). Nesta empreitada, esbarrei com o [artigo sobre traduções do Django](https://docs.djangoproject.com/en/1.4/topics/i18n/translation/), uma vez que o mesmo oferece suporte completo à [internacionalização e localização](https://en.wikipedia.org/wiki/Internationalization_and_localization).

O processo como um todo é muito simples e não se difere da forma como muitos softwares são traduzidos, utilizando a ferramenta [gettext](https://www.gnu.org/software/gettext/), largamente conhecida no mundo Unix (se você usa o Windows, [este artigo pode lhe ajudar a instalá-la](https://docs.djangoproject.com/en/1.4/topics/i18n/translation/#gettext-on-windows)), e se baseia em três etapas:

* Utilizar as funções da biblioteca gettext em todas as strings quais são passíveis de tradução, ainda no código-fonte.  
* Gerar um arquivo texto com estas strings, adicionando em seguida suas respectivas traduções.  
* Compilar este arquivo texto para um formato binário otimizado para ser utilizado pela aplicação.

A primeira parte, é claro, fica a cargo dos autores da aplicação. O trabalho de tradução começa mesmo na segunda etapa. Na verdade você pode gerar um só arquivo de tradução de todo seu projeto Django, incluindo todas as suas aplicações, mas fica mais fácil gerenciá-lo executando o processo de forma individual. Para isto, basta criar uma pasta chamada "*locale*", contendo um diretório com o [código da língua](https://docs.djangoproject.com/en/1.4/topics/i18n/#term-locale-name) qual a app será traduzida (como "*pt_BR*"), que por sua vez tem um diretório chamado "*LC_MESSAGES*", como por exemplo:

    [myhro@squeeze:~/django/emailusernames]$ mkdir -p locale/pt_BR/LC_MESSAGES/
    [myhro@squeeze:~/django/emailusernames]$ tree
    (...)
    ├── locale
    │   └── pt_BR
    │       └── LC_MESSAGES
    (...)

Esta estrutura é importante para que o Django reconheça corretamente os arquivos de tradução. A partir deste ponto, você pode gerar o arquivo contendo as strings no idioma original, que neste caso é o inglês, qual contém alguns detalhes como nome do arquivo e linha qual se está traduzindo:

    [myhro@squeeze:~/django/emailusernames]$ django-admin.py makemessages -a
    processing language pt_BR

O parâmetro "**-a**" executa este processo para todos os diretórios de línguas previamente criadas, o que no nosso caso foi apenas o português brasileiro. Caso você esteja lidando com mais de um idioma, pode gerar para apenas um deles, mais especificamente, com o parâmetro "**-l [código_da_língua]**", como em "**-l pt_BR**". Em seguida, abra o arquivo gerado, cujo nome é "*django.po*", e você encontrará, além do cabeçalho, linhas como:

{% highlight po %}
(...)
#: admin.py:26
msgid "Groups"
msgstr ""

#: forms.py:10
msgid "Please enter a correct email and password. "
msgstr ""

#: forms.py:11
msgid "You do not have permission to access the admin."
msgstr ""
(...)
{% endhighlight %}

E a única coisa que você precisa fazer é traduzir cada uma das "*msgid*" em sua respectiva "*msgstr*", por exemplo:

{% highlight po %}
(...)
#: admin.py:26
msgid "Groups"
msgstr "Grupos"

#: forms.py:10
msgid "Please enter a correct email and password. "
msgstr "Por favor digite corretamente o email e a senha."

#: forms.py:11
msgid "You do not have permission to access the admin."
msgstr "Você não tem permissão para acessar a área administrativa."
(...)
{% endhighlight %}

Extremamente simples, não? Só tome cuidado para salvar o arquivo como [UTF-8](https://en.wikipedia.org/wiki/UTF-8) (o que [deve ser feito em qualquer situação](http://www.utf8everywhere.org/)), de forma a evitar possíveis problemas com a acentuação. Agora só falta a última parte, que é efetivamente compilar as traduções efetuadas, para que assim elas possam ser utilizadas pelo Django:

    [myhro@squeeze:~/django/emailusernames]$ django-admin.py compilemessages
    processing file django.po in /home/myhro/django/emailusernames/locale/pt_BR/LC_MESSAGES
    [myhro@squeeze:~/django/emailusernames]$ file locale/pt_BR/LC_MESSAGES/django.*
    locale/pt_BR/LC_MESSAGES/django.mo: GNU message catalog (little endian), revision 0, 1 messages
    locale/pt_BR/LC_MESSAGES/django.po: UTF-8 Unicode English text

E as mensagens desta app estarão magicamente traduzidas de agora em diante. Caso isto não aconteça, execute novamente o comando "*manage.py runserver*" e verifique se a variável "**LANGUAGE_CODE**" não está definida como "**en-us**" no arquivo "*settings.py*" do Django.
