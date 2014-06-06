---
date: 2014-06-06 19:20
layout: post
title: "Filas assíncronas no Django com o Redis Queue"
categories:
- django
- redis
---

Tenho duas grandes paixões nesta vida: mulheres bonitas e cheirosas e softwares que resolvem um problema de forma magistral, embora excepcionalmente simples. Como exemplares da primeira opção andam escassos em meu cotidiano, falarei hoje sobre o [Django-RQ][django-rq]. Este, como o próprio nome sugere, facilita a integração do [Django][django] com o [RQ (Redis Queue)][rq], e foi recentemente apresentado a mim por [Herberth Amaral][herberthamaral] (indivíduo que mesmo trabalhando 20h por dia ainda tem tempo para descobrir coisas novas).

Os porquês de se usar filas de processamento assíncrono/paralelo são largamente conhecidos e o Fabio Akita fez uma análise excelente, como sempre, [neste artigo em seu blog][solucoes-mundo-assincrono]. Resumidamente, no caso de aplicações web, você realizaria o processamento em background de qualquer ação qual a requisição do usuário não dependa para ser completada, como redimensionar uma imagem após seu upload ou enviar um e-mail de confirmação após um cadastro.

No ecossistema Python, a ferramenta padrão _de facto_ utilizada para isto é o [Celery][celery], qual fiz uso por bastante tempo antes de conhecer o Django-RQ. O Celery é muito bom e funciona forma satisfatória, mas é um pouco lerdo para subir/matar seus workers e mais complicado do que deveria, até mesmo para começar a utilizá-lo pela primeira vez. Uma vantagem dele é que a comunicação `aplicação -> fila -> workers` pode ser feita utilizando o banco de dados do próprio Django, sem dependências externas. No caso do Django-RQ, precisamos do [Redis][redis], o banco de dados [NoSQL][nosql] mais popular do mundo, algo que não chega a ser necessariamente uma complicação.

A instalação e utilização do Django-RQ está descrita de forma bem objetiva em seu [README][readme]. O que você precisa fazer, basicamente, é adicionar as configurações de acesso ao Redis no seu `settings.py` e, a cada vez que fosse chamar uma função cuja execução você espera que seja demorada, por motivos de CPU ou I/O, enfileiraria sua chamada. Os workers quais estiverem rodando ou forem criados posteriormente passarão a consumir as chamadas armazenadas na fila, processando-as de forma assíncrona.

O detalhe que me deixou verdadeiramente satisfeito e ao mesmo tempo completamente impressionado é muito simples, porém extremamente útil: se a execução de algum dos jobs (nome dado a cada um dos itens da fila) falhar, estes serão armazenados em uma fila separada, podendo ser re-enfileirados com apenas um botão na [interface de administração do Django][django-admin]. Desta forma, você pode corrigir o problema qual causou o erro para que estes possam ser devidamente processados em uma próxima oportunidade, sem que os dados sejam descartados pela ocorrência da falha.

[celery]: http://www.celeryproject.org/
[django-admin]: https://docs.djangoproject.com/en/1.6/ref/contrib/admin/
[django-rq]: https://github.com/ui/django-rq
[django]: https://www.djangoproject.com/
[herberthamaral]: https://twitter.com/herberthamaral
[nosql]: https://en.wikipedia.org/wiki/NoSQL
[readme]: https://github.com/ui/django-rq/blob/master/README.rst#installation
[redis]: http://redis.io/
[rq]: https://github.com/nvie/rq
[solucoes-mundo-assincrono]: http://www.akitaonrails.com/2013/12/23/solucoes-para-um-mundo-assincrono-concorrente
