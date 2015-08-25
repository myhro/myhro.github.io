---
date: 2012-09-21 19:14:41
layout: post
title: Problemas recentes com o Myhro.net
categories:
- myhro.net
- php
---

Talvez você não tenha conseguido acessar o [Myhro.net](http://myhro.net/) nos últimos dias, pois além do tempo necessário para propagação das entradas DNS após a mudança de servidor, houve uma espécie de ataque sendo realizado contra o mesmo. URLs indesejadas sempre foram encurtadas lá e por conta disso, por diversas vezes no decorrer da semana costumo dar uma olhada nos últimos endereços adicionados. Isso nunca tinha chegado a ser um problema... Até essa semana.

Tudo começou com alguns links encurtados por russos e ucranianos, com destino a sites utilizados somente com o intuito de fazer propaganda de outros. Sempre tento, ao invés de remover o link em questão, redirecionar a URL para outra inexistente, uma vez que o link encurtado gerado não será reutilizado de qualquer forma. O problema se tornou mais grave quando um norte-americano desocupado encurtou mais de 30 (trinta!) URLs de fotos, muitas das quais contendo meninas que dificilmente se aproximam dos 18 anos de idade.

Como não se trata de um ataque realmente automatizado, como os scripts que costumam fazer spam nos comentários do blog, a situação é um pouco mais complicada para ser resolvida. Não basta, por exemplo, colocar um [CAPTCHA](https://en.wikipedia.org/wiki/CAPTCHA) no formulário disponível no site. Após traçar a origem dos mesmos e perceber que virtualmente 100% dos links encurtados indevidamente foram feitos por usuários de fora do Brasil, quais não são o público alvo de um site inteiramente em português, não tive outra alternativa senão bloquear todos os acessos de visitantes estrangeiros.

Em um primeiro momento, o bloqueio foi implementado a nível do servidor HTTP, que [como já foi dito aqui há alguns meses](http://blog.myhro.info/2012/07/myhro-blog-agora-via-https/), é o [nginx](http://nginx.org/). O problema é que esta abordagem não é muito flexível, já que por mais que fosse possível restringir apenas a criação de novas URLs (e não acesso às quais já existem), isso não é assim tão simples de ser feito. Uma solução mais elegante foi fazê-lo a nível de aplicação, utilizando a biblioteca [GeoIP](http://www.maxmind.com/app/php).

O mais surpreendente é que, num primeiro momento, cheguei a imaginar que não seria tão simples implementar esta solução, mas rapidamente percebi que, pra minha sorte, estava enganado. No Debian basta instalar os pacotes [php5-geoip](http://packages.debian.org/squeeze/php5-geoip) e [geoip-database](http://packages.debian.org/squeeze/geoip-database) (embora recomende que você [baixe manualmente uma versão mais nova do banco de dados](http://www.howtoforge.com/nginx-how-to-block-visitors-by-country-with-the-geoip-module-debian-ubuntu), pois a versão que acompanha o pacote provavelmente estará desatualizada), já que este segundo não é dependência do primeiro. A partir daí, basta utilizar as [funções da biblioteca GeoIP](http://php.net/manual/en/ref.geoip.php), como a [geoip\_country\_code\_by\_name()](http://php.net/manual/en/function.geoip-country-code-by-name.php), que retorna o código [ISO 3166-1](http://en.wikipedia.org/wiki/ISO_3166-1) com as duas letras correspondentes ao país do IP/Hostname.
