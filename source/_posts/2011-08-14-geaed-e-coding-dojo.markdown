---
date: 2011-08-14 11:37:43
layout: post
slug: geaed-e-coding-dojo
title: GEAED e Coding Dojo
categories:
- programacao
- python
---

Ontem o GEAED fez sua primeira reunião no estilo "[Coding Dojo](http://pet.inf.ufsc.br/dojo/o-que-eh-dojo/)", embora ainda não obedecendo o formato fielmente. Utilizamos [pair programming](http://xp.edugraf.ufsc.br/bin/view/XP/PairProgramming) e uma parte do tempo exclusivamente para [refatoração do código](http://www.inf.ufsc.br/~herb/disc/engenharia_de_software/). Só faltou mesmo o [TDD](http://www.franciscosouza.com.br/2009/07/26/pyunit-test-drive-development-tdd-na-pratica/), que não foi utilizado de imediato por não termos prática nesta técnica. Desta forma, poderíamos acabar consumindo mais tempo da nossa única reunião semanal (atualmente) com os testes, ao invés de nos concentrarmos na resolução dos problemas.

O mais interessante (e que pôde ser facilmente notado) é que a nossa produtividade aumentou bastante ao utilizarmos Python ao invés de C para implementação dos algoritmos. Não tem coisa mais satisfatória para um programador do que poder se dedicar exclusivamente à resolução do problema proposto, sem se preocupar com detalhes que não o competem. Utilizando Python, a linguagem faz o máximo que pode por você. Seu único trabalho é conhecê-la, modelando de forma lógica e correta o fluxo de execução do programa, buscando sempre a maneira mais simples de fazê-lo.

É um tanto quanto complicado falar assim do C, que é muito provavelmente a mais importante linguagem de programação já inventada. Não tenho direito de falar mal da [própria linguagem qual o interpretador Python foi escrito](http://wiki.python.org/moin/CPython). C é importantíssimo para se aprender e ensinar computação, com detalhes em um nível que você não veria em outras linguagens, como o próprio Python. Mesmo assim, eu dificilmente trabalharia com ela profissionalmente. Admiro muito a linguagem e mais ainda [quem de fato faz coisas decentes](http://www.itarare.sp.gov.br/pmdi/index.php/software-livre/211-quem-inus-torvalds.html) com a mesma, mas prefiro algo que me estresse menos no meu dia-a-dia.

Os problemas abordados foram os seguintes (em ordem cronológica):
	
* [Dígitos verificadores do CPF](http://www.geradorcpf.com/algoritmo_do_cpf.htm) 
* [Algarismos Romanos](http://codingdojo.org/cgi-bin/wiki.pl?KataRomanNumerals) 
* [Jokenpo](http://dojopuzzles.com/problemas/exibe/jokenpo/) (este último mais pela diversão do que pelo problema).

Observações:
	
* Devemos nos atrasar menos, já que sempre temos perdido ao menos meia hora esperando que todos estejam prontos.  
* Precisamos de nomes mais explícitos para nossas variáveis.

Os códigos estão disponíveis, como sempre, no [nosso repositório do GitHub](https://github.com/myhro/GEAED). A implementação do algoritmo dos dígitos verificadores do CPF tem o código pré-refatoração comentado, deixando visível a mudança para um código bem mais "[pythônico](http://faassen.n--tree.net/blog/view/weblog/2005/08/06/0)". Como um famoso programador certa vez disse:

> 
> "Keep refactoring as Johnnie keep walking."
([AMARAL, Herberth](http://herberthamaral.com/))
> 
> 
