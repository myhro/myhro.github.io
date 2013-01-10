---
date: 2012-07-16 11:05:51
layout: post
slug: myhro-blog-agora-via-https
title: Myhro Blog agora via HTTPS
categories:
- seguranca
- servidor
---

Desde o dia 11 deste mês, uma quarta-feira, o Myhro Blog está disponível apenas via HTTPS. Minha intenção, na verdade, era ter feito isso há alguns meses, mas o bendito processo de certificação da [StartSSL](https://www.startssl.com/), que fornece [certificados SSL gratuitamente para uso pessoal](https://www.startssl.com/?app=1), nem sempre funciona. Tive de tentar diversas vezes, inclusive correndo o risco de ser banido pra sempre por duplicidade de cadastro, até conseguir. A parte mais interessante do processo é que não há a criação ou utilização de senhas: você recebe um certificado que é instalado no seu navegador, autenticando seu endereço de e-mail, e o utiliza para logar no painel de controle do site.

Embora o objetivo primordial do certificado [SSL](https://en.wikipedia.org/wiki/Transport_Layer_Security) fosse tornar segura a utilização do painel de administração do Wordpress (sem ter de ficar [tunelando a conexão via SSH](http://blog.myhro.info/2012/04/tornando-o-uso-do-ssh-mais-simples-e-agradavel/)), um efeito colateral inesperado e muito bem vindo ocorreu: a diminuição brusca do número de SPAMs recebidos via comentários. Por mais eficiente que seja o [Askimet](https://akismet.com/) (meu blog já recebeu milhares de comentários devidamente marcados como SPAM e os que não foram corretamente identificados podem ser contados com os dedos de uma só mão), é chato ficar limpando a lista de "lixo eletrônico", e o que é pior, já que mesmo apagados, os comentários continuam ocupando espaço no banco de dados até que você dê um "[optimize](https://dev.mysql.com/doc/refman/5.5/en/optimize-table.html)" na tabela.

A explicação do o fenômeno é bem simples: os bots menos sofisticados, que varrem a internet procurando blogs do wordpress para fazer SPAM via comentários, não são capazes de se conectar via HTTPS, seja por uma simples questão de portas ou limitações quanto a implementação do protocolo. E a melhor parte é que, pelo menos entre os bots que visitam o Myhro Blog, eles são a maioria. Ainda não posso dizer que o problema do SPAM foi completamente erradicado apenas com a migração para HTTPS, mas sim que foi reduzido para quase zero. Sendo assim, a recomendação que posso lhe fazer é: se você sofre com SPAM no Wordpress, instale um certificado SSL no servidor do seu blog o mais rápido possível.

[O nginx está redirecionando quaisquer requisições HTTP para sua URL correspondente em HTTPS](http://serverfault.com/questions/67316/in-nginx-how-can-i-rewrite-all-http-requests-to-https-while-maintaining-sub-dom), principalmente para não quebrar os links antigos. As únicas modificações realizadas manualmente foram na origem das imagens embutidas nos posts. Isto não era exatamente uma necessidade, mas sem isto os navegadores ficavam acusando "conteúdo inseguro" na página. Nos testes realizados não houveram problemas aparentes, mas se por algum acaso vocês encontrarem algo que não está funcionando como deveria por aqui, como links quebrados ou imagens ausentes, peço que por favor me avisem. O Myhro Blog está sempre inovando para melhor atendê-los.
