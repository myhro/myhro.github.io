---
date: 2014-08-03 11:02
layout: post
title: "Detecção de IP por trás de um proxy reverso com o Django"
categories:
- django
- nginx
---

A pilha de serviços mais comum em um ambiente de produção de uma aplicação Django hoje consiste basicamente em:

* Um servidor WSGI, como o [Gunicorn][gunicorn] ou o [uWSGI][uwsgi], responsável por servir sua aplicação.
* Um proxy reverso, como o [nginx][nginx], servindo arquivos estáticos e passando todas as outras requisições para o servidor de aplicação.

Esta é uma solução bacana, eficiente, escalável e não há nada de errado com isso. Entretanto, precisamos tomar um certo cuidado ao realizar ações que envolvem a detecção de IP do cliente qual está acessando a aplicação. Quando não temos um proxy reverso na frente, basta verificar o conteúdo do `request.META['REMOTE_ADDR']` para descobrirmos o IP qual originou a requisição. Do contrário, a situação muda de figura.

Quando o proxy reverso recebe uma requisição do cliente e a encaminha para o servidor de aplicação, o IP de origem da requisição passa a ser o do próprio proxy reverso. Chegamos em uma situação onde, por exemplo, para a aplicação todos as requisições parecem vir de um mesmo cliente, com mesmo IP. Para contornar esta situação, o padrão _de facto_ é utilizar o cabeçalho [X-Forwarded-For][x-forwarded-for] do protocolo HTTP, qual indica que se trata de uma requisição previamente encaminhada.

Procurando sobre como detectar o IP por trás de um proxy reverso com o Django no Google, você poderia se deparar com [este trecho de código][get_addr-snippet], qual executa uma simples verificação retorna o IP detectado. A fim de simplificar a explicação do problema, vamos utilizar uma versão reduzida do mesmo como exemplo, qual basicamente verifica se o cabeçalho está presente e extrai o primeiro IP do campo. Caso contrário, retorna apenas o `REMOTE_ADDR`:

{% codeblock lang:python %}
def get_client_ip(request):
    ip = request.META.get('HTTP_X_FORWARDED_FOR')
    if ip:
        return ip.split(', ')[0]
    else:
        return request.META['REMOTE_ADDR']
{% endcodeblock %}

Imediatamente, podemos levantar a seguinte pergunta: _como assim o **primeiro** IP?!_

Isto acontece pois caso já exista uma entrada `X-Forwarded-For` no cabeçalho, ao encaminhar a requisição o nginx irá concatenar o IP detectado do cliente **ao final** da string deste campo, resultando em dois IPs ao invés de um só. Portanto, o correto é pegar o último endereço extraído e não o primeiro, pois do contrário o cliente poderia facilmente forjar seu IP:

{% codeblock lang:python %}
    (...)
    if ip:
        return ip.split(', ')[-1]
    (...)
{% endcodeblock %}


[gunicorn]: http://gunicorn.org/
[uwsgi]: http://projects.unbit.it/uwsgi/
[nginx]: http://nginx.org/
[x-forwarded-for]: https://en.wikipedia.org/wiki/X-Forwarded-For
[get_addr-snippet]: https://djangosnippets.org/snippets/2863/
