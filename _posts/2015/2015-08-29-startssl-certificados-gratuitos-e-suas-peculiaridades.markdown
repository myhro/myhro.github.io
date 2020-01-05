---
date: 2015-08-29 08:36:32
layout: post
title: "StartSSL: certificados gratuitos e suas peculiaridades"
...

A [StartCom][startcom] é uma empresa israelense, fundada em 1999 e sediada em [Eilat][eilat], que dentre suas atividades trabalha com certificados SSL/TLS sob a marca [StartSSL][startssl]. Ela não seria muito diferente de qualquer outra empresa que comercializa produtos parecidos se não tivesse um diferencial: seu certificado SSL "Class 1" é oferecido gratuitamente, desde que sua finalidade seja voltada para uso pessoal. Não é necessário nada mais além de validar seu e-mail, ao criar uma conta com eles, e seu domínio (o que também é feito por e-mail), ao emitir um certificado. O primeiro passo, especificamente, é feito de uma maneira não muito convencional.

Logo de imediato é possível perceber o quanto o processo de registro é diferente: não há a criação de um usuário e senha. Ao se cadastrar, é enviado um e-mail com um código que deve ser colado na tela pós-cadastro do site deles. Não há sequer um link que o leve à mesma janela de confirmação, que não pode ser fechada enquanto este código não for submetido. Após confirmado, será exibida uma mensagem dizendo que a conta será validada manualmente antes que possa ser efetivamente utilizada. Não levei isso muito a sério, pois uma vez efetuei o registro entre 6 e 7 da manhã (horário de Israel) e mesmo assim validaram a conta em pouco mais do que 5 minutos.

Quando confirmarem a requisição de criação da conta, um segundo e-mail será enviado. Desta vez haverá um link que o levará para o processo registro propriamente dito, que consiste basicamente em gerar um [certificado S/MIME de autenticação do cliente][ibm-ssl-auth]. O processo é realizado de forma automática através de um _wizard_ "_Continue/Install/Finish_" e ao final você estará automaticamente logado no sistema. Neste momento, no gerenciador de certificados do navegador (no Firefox: _Preferences_ -> _Advanced_ -> _Certificates_ -> _View Certificates_) haverá um certificado emitido pela StartCom Ltd. que o identifica como cliente.

Este certificado, composto também por sua chave privada, identifica e autoriza o navegador a se logar no painel de controle da StartSSL. É muito importante que você não o perca de forma alguma, pois do contrário a única forma de se autenticar novamente será criar uma nova conta e entrar em contato com o suporte, tentando convencê-los a associá-la com a conta antiga, conforme [recomenda o FAQ][startssl-faq-lost-cert]. Utilize o recurso de _Backup_/_Export_ do gerenciador de certificados do navegador e armazene uma cópia, protegida por senha, em um local seguro.

Terminado o processo de registro, é necessário validar a propriedade de um domínio antes de emitir um certificado. Os passos são muito simples, consistindo em informar o domínio e um e-mail associado ao mesmo, como os genéricos "_postmaster@example.com_" e "_webmaster@example.com_", ou algum outro endereço encontrado em seu WHOIS. Um código será enviado por e-mail e, confirmando-o, a validação será efetuada. A partir deste ponto, por até 30 dias será possível emitir certificados para este domínio sem precisar validá-lo novamente.

Passadas estas etapas, é possível enfim emitir o certificado SSL desejado. Acessando a opção _Certificates Wizard_ -> _Web Server SSL/TLS Certificate_ do painel, é possível realizar todo o processo de geração através da interface web. Caso você seja um _sysadmin_ "_old school_", preferirá pular a opção de geração de chave privada, gerando esta e um CSR (_Certificate Signing Request_) com o [OpenSSL][openssl] através do terminal, o que pode ser feito [em um só comando][namecheap-openssl]:

    openssl req -new -newkey rsa:2048 -nodes -keyout server.key -out server.csr

Respondendo as perguntas, serão gerados dois arquivos: o `server.key`, correspondente a chave privada do certificado, e o `server.csr`, que como a própria sigla diz, é a requisição de assinatura de um certificado. Este último é o único que precisa ser enviado para qualquer empresa que trabalhe com geração de certificados. [A chave é realmente privada][tldp-ssl] e sem ela o servidor web não poderá utilizar o certificado a ser assinado. Somente quem possuir ambos os arquivos poderá servir conexões HTTPS a partir do domínio.

Continuando o _wizard_, será perguntado qual o subdomínio a ser utilizado. O certificado irá valer tanto para este subdomínio quanto para o domínio em si. Isto é, um certificado criado para o "_www.example.com_" também vale para "_example.com_". Ao final destas etapas, estarão disponíveis três certificados que precisarão serem concatenados em um só arquivo. São eles:

* O certificado do domínio/subdomínio em si;
* O certificado intermediário;
* O certificado raiz.

Esta relação é o que fornece a validade ao certificado do final. De cima pra baixo, cada certificado possui uma assinatura do próximo elo da corrente. É por conta disto que cada sistema operacional ou navegador não precisa conhecer todos os certificados SSL existentes, basta que estes sejam assinados por outro certificado previamente conhecido e confiado como válido. Vale ressaltar ainda que a ordem dos certificados no arquivo que combina todos eles tem de ser a mesma da lista acima.

Como não poderia deixar de ser, o certificado gratuito da StartSSL possui [algumas limitações][startssl-limits]:

* A duração máxima do certificado é de um ano (embora seja possível emitir um novo a qualquer momento);
* O certificado vale para apenas um domínio/subdomínio: nada de _wildcards_ ou múltiplos subdomínios (isto pode ser resolvido emitindo-se mais de um certificado);
* Embora realmente seja um produto grátis, há um valor a ser pago (US$ 24.90) caso seja necessário [revogar o certificado][startssl-revocation], como no caso de uma chave privada comprometida;
* O uso do certificado para fins comerciais é vetado.

Para a grande maioria dos casos de uso, nenhuma destas limitações é realmente crítica a ponto de inviabilizar sua utilização. O comportamento mais incômodo da empresa é mesmo a janela de manutenção, onde toda madrugada de sexta para sábado o site fica indisponível:

> **Weekend Maintenance**
>
> Some of our services are offline and under maintenance during the night hours on weekends until 7:00 AM GMT in the morning. We apologize for the temporary inconvenience and thank you for your understanding.

Estando ciente destes detalhes e tomando cuidado para não perder o certificado do cliente necessário para acessar o painel de controle, a StartSSL oferece um produto diferenciado e que realmente funciona, sem custo algum. É uma boa alternativa enquanto o projeto [Let's Encrypt][letsencrypt], que também irá oferecer certificados gratuitos, [não é lançado][letsencrypt-schedule]. Há ainda a opção de [certificados _wildcard_ ou EV][startssl-products] (_Extended Validation_, aqueles cujo nome da empresa aparece em destaque na barra de endereços) pagos, caso estes sejam uma necessidade.

[eilat]: https://en.wikipedia.org/wiki/Eilat
[ibm-ssl-auth]: http://www.ibm.com/developerworks/lotus/library/ls-SSL_client_authentication/
[letsencrypt-schedule]: https://letsencrypt.org/2015/08/07/updated-lets-encrypt-launch-schedule.html
[letsencrypt]: https://letsencrypt.org/
[namecheap-openssl]: https://www.namecheap.com/support/knowledgebase/article.aspx/9446/0/apache-opensslmodsslnginx
[openssl]: https://www.openssl.org/
[startcom]: https://www.startcom.org/
[startssl-faq-lost-cert]: https://www.startssl.com/?app=25#14
[startssl-limits]: https://en.wikipedia.org/wiki/StartCom#Limitations_of_StartSSL_Free
[startssl-products]: https://www.startssl.com/?app=39
[startssl-revocation]: https://www.startssl.com/?app=25#72
[startssl]: https://www.startssl.com/
[tldp-ssl]: http://www.tldp.org/HOWTO/SSL-Certificates-HOWTO/x64.html
