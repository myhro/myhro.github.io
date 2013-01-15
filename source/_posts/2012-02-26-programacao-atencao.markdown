---
date: 2012-02-26 23:17:30
layout: post
slug: programacao-atencao
title: Programação == Atenção
categories:
- c
- programacao
---

Quando ainda engatinhava no vasto mundo dos algoritmos e das linguagens de programação, um dos primeiros materiais que li a respeito (não me lembro exatamente qual) fazia um alerta quanto a necessidade de atenção por parte do programador. Achei curioso, por mais que hoje me pareça óbvio. Lendo outros livros já voltados a programadores mais experientes, continuei encontrando citações parecidas, embora não com as mesmas palavras. Sempre ficava me perguntando se alguém pode se aventurar como programador sem estar ciente de que sua maior virtude teria de ser a atenção; se realmente havia a necessidade de lembrá-lo que este jamais deveria parar de se preocupar com detalhes.

Alguns dias atrás, participei de uma reunião do [Grupo de Estudos em Jogos](http://games.geaed.org/) liderado pelo meu prezado colega, de curso e de trabalho, [@mbodock](http://twitter.com/mbodock). Ao ver algumas operações amontoadas em um _if_, perguntei se não seria mais prudente utilizar parênteses para separá-las, não só por legibilidade (o que é tão importante quanto sua execução correta), mas como garantia que a precedência dos operadores seria avaliada como desejado. Pode parecer algo extremamente bobo e desnecessário, mas você sabia que não é necessário se preocupar com isso se passar a usar parênteses? Pra que decorar a ordem de precedência dos 15 operadores do C (ou [18 do C++](http://www.cplusplus.com/doc/tutorial/operators/)), se você pode reduzi-la a duas regras: multiplicação e divisão vem antes da adição e subtração; para todos os outros utiliza-se parênteses.

Programar é difícil, principalmente porque pensar logicamente é difícil. Com prática o processo pode se tornar menos árduo, principalmente quando você se depara com problemas já enfrentados, mas continua sendo difícil. Isto, entretanto, não significa que você não deva fazer o possível para tornar seus programas mais simples. Não só porque muitas vezes não é só você quem vai utilizá-los, do ponto de vista da interface ou do reuso de código, mas porque outras vezes você não pode sequer retomar o raciocínio que estava na sua cabeça quando escreveu aquela função sem comentário algum.

Se você programa, por hobby ou profissão, seja atencioso com seu código. Não deixe de tornar mais claro um trecho do mesmo por preguiça ou por orgulho (o clássico: "se foi difícil para escrevê-lo, que seja mais ainda para lê-lo"). Comentários, que são a forma mais primitiva de documentação, são chatos porém necessários (algo que eu mesmo ignoro às vezes e estou tentando mudar). Indentação não é firula, é um processo extremamente simples de se organizar blocos de código na cabeça de quem o lê. Para o computador pouco importa se você indentou corretamente seu código ou colocou tudo em uma linha só. Exceto se você estiver programando em Python.

Referências:  
OUALLINE, Steve. [Practical C Programming](http://shop.oreilly.com/product/9781565923065.do), 3rd Edition. Cambridge, MA: O'Reilly Media, 1997.
