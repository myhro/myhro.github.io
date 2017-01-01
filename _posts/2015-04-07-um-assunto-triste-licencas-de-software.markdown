---
date: 2015-04-07 23:03
layout: post
title: "Um assunto triste: licenças de software"
...

Um tema bastante delicado, triste e por vezes quase incompreensível no mundo do software são suas licenças. Se trata de um assunto tão complexo, que nem mesmo as próprias licenças de software livre resolvem isso de maneira simplificada. É possível que [duas licenças de software livre não sejam compatíveis entre si][free-license-compatibility] e, pasmem, nem mesmo entre duas versões dela mesma. Isto é tão absurdo que a própria [Free Software Foundation][fsf] destaca na [descrição da GPLv3][gnu-gpl]:

> "Please note that GPLv3 is not compatible with GPLv2 by itself."

Um caso curioso e que serve perfeitamente para exemplificar algumas destas limitações é o [MongoDB][mongodb], um banco de dados NoSQL orientado a documentos. O servidor do MongoDB e suas ferramentas de administração são [disponibilizados através da licença GNU Affero GPL v3.0][mongodb-licensing]. A principal diferença entre a GPL e a AGPL é que esta última considera a utilização em rede uma forma de distribuição - algo a ser considerado no caso de um sistema de gerenciamento de banco de dados.

Agora, a parte curiosa é que os *drivers* oficiais do MongoDB, isto é, as bibliotecas utilizadas em diversas linguagens de programação para comunicação com o servidor de banco de dados, são [distribuídos sob a licença Apache License v2.0][mongodb-licensing]. E por que isto acontece? Porque a licença Apache permite que um sistema proprietário incorpore seu código sem a obrigação trazida pela [cláusula de reciprocidade][reciprocity], presente em outras licenças como as já citadas GPL e AGPL.

A escolha de uma licença de software livre adequada é uma tarefa tão complicada que o próprio [GitHub][github] lançou, em Julho de 2013, [uma campanha para auxiliar os desenvolvedores com isto][gh-choosealicense]: o [ChooseALicense.com][choosealicense], um site que basicamente resume as licenças mais populares para que o processo se torne menos problemático. A iniciativa surtiu efeito, mas ainda hoje [menos de 20% dos repositórios do GitHub][gh-license-stats] possuem uma licença definida.

Quando entramos no campo das licenças de software proprietário, a situação ainda piora. Você sabia que quando o Google Chrome foi lançado, em 2008, seu EULA (*End-User License Agreement*) dizia que [tudo o que fosse transmitido pelo navegador, seja enviado ou recebido, pertencia ao Google][chrome-eula]? Por fim corrigiram os termos e alegaram que tudo não passou de uma confusão, mas é impossível não relacionar o acontecido com [o episódio de South Park][humancentipad] onde o Kyle não leu a licença do iTunes ao aceitá-la, o que permitiu a Apple utilizá-lo em uma experiência para desenvolver uma [centopéia humana][human-centiped].

Algumas licenças são tão restritivas que praticamente impossibilitam o uso de um software, como é o caso do [OCI8][oci8], uma biblioteca que permite a conexão de um sistema em PHP, uma linguagem livre, com uma instância do Oracle DB, um banco de dados proprietário. Para compilar o OCI8, [é necessário baixar as bibliotecas que acompanham o cliente][oci8-requirements] (algo entre 30-60MB) distribuído pela Oracle, mas não sem antes aceitar os termos de uso e se registrar no site, uma vez que o download anônimo não é permitido. É sério que consideram isso razoável somente para permitir conexão a um banco de dados que já é pago?

[Jeff Atwood][jeff-atwood], criador da rede [Stack Exchange][stack-exchange] ([Stack Overflow][stack-overflow], [Server Fault][server-fault], [Ask Ubuntu][ask-ubuntu], etc.), [já comentava sobre isso][pick-license] muitos anos atrás: licenças de software são um campo minado, onde sua cabeça dói e você pode até ter vontade de morrer quando reflete a respeito. Se o mundo fosse um lugar mais agradável e com menos burocracia, todos os softwares existentes seriam distribuídos sob licença [WTFPL][wtfpl].

[ask-ubuntu]: http://askubuntu.com/
[choosealicense]: http://choosealicense.com/
[chrome-eula]: http://gizmodo.com/5044871/google-chrome-eula-claims-ownership-of-everything-you-create-on-chrome-from-blog-posts-to-emails
[free-license-compatibility]: https://en.wikipedia.org/wiki/GNU_General_Public_License#Compatibility_and_multi-licensing
[fsf]: https://www.fsf.org/
[gh-choosealicense]: https://github.com/blog/1530-choosing-an-open-source-license
[gh-license-stats]: https://github.com/blog/1964-open-source-license-usage-on-github-com
[github]: https://github.com/
[gnu-gpl]: https://www.gnu.org/licenses/license-list.html#GPLCompatibleLicenses
[human-centiped]: http://www.imdb.com/title/tt1467304/
...
[humancentipad]: https://en.wikipedia.org/wiki/HumancentiPad
[jeff-atwood]: http://blog.codinghorror.com/about-me/
[mongodb-licensing]: https://www.mongodb.org/about/licensing/
[mongodb]: https://www.mongodb.org/
[oci8-requirements]: https://php.net/manual/en/oci8.requirements.php
[oci8]: https://php.net/manual/en/intro.oci8.php
[pick-license]: http://blog.codinghorror.com/pick-a-license-any-license/
[reciprocity]: https://en.wikipedia.org/wiki/Copyleft#Reciprocity
[server-fault]: http://serverfault.com/
[stack-exchange]: http://stackexchange.com/sites
[stack-overflow]: http://stackoverflow.com/
[wtfpl]: http://www.wtfpl.net/txt/copying/
