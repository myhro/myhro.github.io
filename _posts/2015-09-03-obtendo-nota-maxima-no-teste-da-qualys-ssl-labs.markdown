---
date: 2015-09-03 21:32:20
layout: post
title: "Obtendo nota máxima no teste da Qualys SSL Labs"
categories:
- seguranca
- ssl
---

Buscando por "[ssl test][google-ssl-test]" no Google, os dois primeiros resultados encontrados correspondem a duas ferramentas muito úteis. Uma, o [SSL Checker][ssl-checker] da [SSL Shopper][ssl-shopper], fornece informações básicas sobre o certificado. Um certificado da [StartSSL][startssl] que tenha sido gerado com as [dicas do último post][post-startssl] resulta em uma saída parecida com esta:

![](/images/2015/ssl-checker.png)

As informações estão dispostas de forma bem didática, mas ainda vale a pena comentar sobre cada item:

* **ssl.myhro.net resolves to 52.2.85.74**: primeiro teste, informando o IP resolvido a partir da requisição de DNS;
* **Server Type: nginx/1.4.6 (Ubuntu)**: informações sobre o servidor, que no caso é o [nginx][nginx] 1.4.6, [versão presente no Ubuntu 14.04][nginx-trusty];
* **The certificate should be trusted by all major web browsers (all the correct intermediate certificates are installed)**: destaca a importância do encadeamento correto dos certificados, com a presença dos certificados intermediários, também citada no [post sobre a StartSSL][post-startssl];
* **The certificate was issued by StartCom**: entidade certificadora responsável por emitir e assinar o certificado;
* **The certificate will expire in 365 days**: tempo restante (em dias) pelo qual o certificado ainda é válido. É possível adicionar um lembrete de expiração, mas normalmente a empresa responsável pela emissão já irá realizar isto quando o prazo estiver vencendo;
* **The hostname (ssl.myhro.net) is correctly listed in the certificate**: informa que o _hostname_ está presente no certificado, o que significa que um browser não irá acusar a divergência de _hostnames_ entre o certificado e a página de fato acessada.

Por fim, há uma representação gráfica da relação entre os certificados, com mais algumas informações quanto ao período de validade, algoritmo de assinatura, etc. O mais importante a se destacar neste momento é que, embora seja uma boa notícia o fato do certificado não ter apresentado problema nesta validação básica, isto não significa que o site por trás dele está realmente seguro. É preciso, portanto, realizar uma análise mais detalhada, como a oferecida pelo [SSL Server Test][ssl-server-test] da [Qualys SSL Labs][qualys-ssl-labs].

O resultado obtido com a configuração padrão do nginx no Ubuntu 14.04 é o seguinte:

![](/images/2015/ssl-test-1.png)

Dois problemas comprometeram a nota do teste: a qualidade da troca de chaves utilizando o protocolo [Diffie-Hellman][dh] e a vunerabilidade à falha de segurança do SSL 3.0 conhecida como [POODLE][poodle]. Felizmente, estas duas fraquezas são simples de serem resolvidas e o próprio teste aponta para documentações relevantes sobre os problemas. 

## Diffie-Hellman

O [guia de implantação do Diffie-Hellman para TLS][dh-guide] traz uma descrição detalhada e até um pouco paranóica (especificando todas as cifras suportadas, o que não é necessário) sobre como reforçar a segurança da troca de chaves. Resumidamente, é preciso realizar dois passos. O primeiro é gerar um grupo de parâmetros com tamanho de 2048 bits:

    openssl dhparam -out dhparams.pem 2048

E em seguida adicionar estas duas linhas ao arquivo de configuração do nginx (seja globalmente ou por _virtual host_):

    ssl_dhparam /etc/nginx/ssl/dhparams.pem;
    ssl_prefer_server_ciphers on;

... Após ter movido o arquivo gerado para o diretório `/etc/nginx/ssl/`.

O resultado ainda continua com classificação `C`, por causa do POODLE, mas não há mais menção ao problema com o protocolo Diffie-Hellman. A nota no quesito "_Protocol Support_" subiu de `70` para `90`:

![](/images/2015/ssl-test-2.png)

## POODLE

Resolver a questão do POODLE é mais fácil ainda, já que basta desabilitar o suporte ao SSL 3.0 no nginx. Por mais estranho que pareça, a configuração padrão do Ubuntu 14.04 adiciona a opção `SSLv3` ao parâmetro `ssl_protocols`. Isto provavelmente se deve ao fato desta versão ter sido lançada em torno de 6 meses antes da descoberta da vulnerabilidade. Para reverter esta configuração, a seguinte linha deve estar presente na configuração do nginx:

    ssl_protocols TLSv1 TLSv1.1 TLSv1.2;

Esta configuração força o nginx a suportar apenas protocolo [TLS (_Transport Layer Security_)][tls] (e não [SSL (_Secure Sockets Layer_)][ssl]), em suas versões de 1.0 a 1.2. Agora, a classificação do teste passa a ser `A` e a nota do "_Protocol Support_" atinge `95`:

![](/images/2015/ssl-test-3.png)

Mesmo assim, ainda há uma pequena melhoria a ser realizada.

## HSTS

O HSTS (_HTTP Strict Transport Security_) é um _header_ adicionado à resposta de uma requisição HTTP que informa ao cliente que todas as conexões a este servidor, dali em diante, devem ser realizadas via HTTPS. O processo para habilitá-lo no nginx consiste em adicionar a seguinte linha ao seu arquivo de configuração:

    add_header Strict-Transport-Security "max-age=31536000; includeSubdomains";

E com isto a classificação sobe para `A+`, ainda que as notas individuais de cada quesito não mudem:

![](/images/2015/ssl-test-4.png)

## Arquivo de configuração do nginx

O arquivo de configuração do _virtual host_ do nginx resultante das modificações realizadas durante os testes é este:

    # HTTPS redirect
    #
    server {
        listen 80;
        server_name ssl.myhro.net;
        return 301 https://ssl.myhro.net$request_uri;
    }

    # HTTPS server
    #
    server {
        listen 443 ssl;
        server_name ssl.myhro.net;
        add_header Strict-Transport-Security "max-age=31536000; includeSubdomains";

        root /usr/share/nginx/html;
        index index.html index.htm;

        ssl on;
        ssl_certificate /etc/nginx/ssl/cert.pem;
        ssl_certificate_key /etc/nginx/ssl/cert.key;
        ssl_dhparam /etc/nginx/ssl/dhparams.pem;

        ssl_session_timeout 5m;

        ssl_protocols TLSv1 TLSv1.1 TLSv1.2;
        ssl_prefer_server_ciphers on;

        location / {
            try_files $uri $uri/ =404;
        }
    }

Vale ressaltar que foi adicionado o redirecionamento `HTTP -> HTTPS`, um comportamento que não é realizado por padrão, mas que é interessantíssimo caso seu interesse realmente seja servir conteúdo de forma segura. Desta forma, todos os visitantes acessarão o site via HTTPS automaticamente.

[dh-guide]: https://weakdh.org/sysadmin.html
[dh]: http://mathworld.wolfram.com/Diffie-HellmanProtocol.html
[google-ssl-test]: https://www.google.com/search?q=ssl+test
[nginx-trusty]: http://packages.ubuntu.com/trusty/nginx
[nginx]: http://nginx.org/
[poodle]: https://en.wikipedia.org/wiki/POODLE
[post-startssl]: /2015/08/startssl-certificados-gratuitos-e-suas-peculiaridades/
[qualys-ssl-labs]: https://www.ssllabs.com/
[ssl-checker]: https://www.sslshopper.com/ssl-checker.html
[ssl-server-test]: https://www.ssllabs.com/ssltest/
[ssl-shopper]: https://www.sslshopper.com/
[ssl]: https://en.wikipedia.org/wiki/Transport_Layer_Security#SSL_1.0.2C_2.0_and_3.0
[startssl]: https://www.startssl.com/
[tls]: https://en.wikipedia.org/wiki/Transport_Layer_Security
