---
date: 2011-12-16 09:54:19
layout: post
title: Armazenando números reais no MySQL
...

Neste semestre letivo que se encerrou na segunda semana de dezembro, nós acadêmicos do então terceiro período do Curso de Sistemas de Informação da Unimontes, passamos praticamente o semestre inteiro fazendo um mesmo trabalho de Linguagens e Técnicas de Programação I. Embora tenha sido extremamente cansativo em alguns momentos (adentrar as madrugadas programando chegaram a ser regra e não exceção), seu desenvolvimento nos trouxe uma experiência considerável em C# e serviu de introdução ao mundo dos bancos de dados. Uma introdução tortuosa, cheia de percalços e feita não da melhor forma possível, mas serviu.

Uma coisa inesperada e ao mesmo tempo extremamente boba surgiu no decorrer do trabalho: o armazenamento de números reais (também conhecidos como ponto flutuante ou "float") não é tão simples quanto parece. Na verdade não é complicado, mas o fato da nossa cultura utilizar a vírgula (e não o ponto) para separar as casas decimais dificulta um pouco as coisas. Seria interessante se o próprio MySQL convertesse o número recebido com vírgula automaticamente, mas na realidade isto não acontece. Tal operação não gera um erro, mas um aviso informando que não é possível trabalhar com o número desta forma, trucando-o e armazenando apenas sua parte inteira.

A partir deste momento, fica a cargo do programador converter o número corretamente antes de efetuar a consulta ao banco de dados. Como uma consulta SQL nada mais é do que uma string devidamente formatada com suas respectivas tabelas, colunas e valores a serem armazenados, o mais intuitivo seria percorrer a string correspondente ao valor e trocar a vírgula "na unha" por um ponto. É o que você faria se estivesse com saudades dos seus tempos de C, mas o processo é bem mais simples em linguagens mais recentes (com menos de 20 anos) como PHP e C#.

Como realizar a conversão em C#:

{% highlight csharp %}
// Adicionando a biblioteca responsável pela localização/globalização:
using System.Globalization;

// Convertendo a vírgula para o ponto. Como o resultado é uma string, é interessante já utilizar a conversão na hora de formatar a query SQL. O "N" informa que queremos o resultado como um número (poderia ser "C" para converter para moeda, já com o cifrão, por exemplo). O segundo parâmetro diz que ele deve ser formatado na notação americana, onde se utiliza ponto como separador de casas decimais:
numero_real.ToString("N", new CultureInfo("en-US"));
{% endhighlight %}

E como fazer a mesma coisa em PHP:

{% highlight php %}
// O primeiro parâmetro é o número a ser convertido, o segundo número de casas decimais, o terceiro o separador das casas decimais e o quarto o separador das casas de milhar (uma string vazia, no caso, já que não queremos nada além do ponto). O retorno também é uma string e não um número:
number_format($numero_real, 2, '.', '');
{% endhighlight %}

Desta forma você não mais precisará se preocupar com seus números reais sendo armazenados incorretamente no banco de dados, nem relembrar como as coisas funcionavam há 30 ou 40 anos atrás.

Referências:  
1. [.NET String.Format() to add commas in thousands place for a number](http://stackoverflow.com/questions/105770/net-string-format-to-add-commas-in-thousands-place-for-a-number)  
2. [A função number_format()](http://imasters.com.br/artigo/448/php/a_funcao_number_format/)  
3. [Currency format for display](http://stackoverflow.com/questions/4842332/currency-format-for-display)  
4. [PHP: number_format](http://www.php.net/manual/en/function.number-format.php)
