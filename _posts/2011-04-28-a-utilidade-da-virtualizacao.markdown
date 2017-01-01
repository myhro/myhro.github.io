---
date: 2011-04-28 10:19:43
layout: post
title: A utilidade da virtualização
...

Não nos surpreendemos ao ouvir alguém dizendo que determinada tecnologia "é o futuro" (embora estas previsões quase sempre não se concretizem). É natural, temos o hábito de tentar prever acontecimentos. Pois este não é o caso da Virtualização: ela é o presente.

O conceito de virtualização é bem simples: utilizar software pra simular hardware. É como rodar um computador dentro de um computador. Note que isto é um tanto quanto diferente do que se tem nos emuladores, que por sua vez simulam uma arquitetura completamente diferente (como um video-game) com perda substancial de desempenho. O mesmo não acontece na virtualização (ao menos não nas mesmas proporções), já que a arquitetura é a mesma ([x86](http://en.wikipedia.org/wiki/X86), por exemplo).

Você poderia se perguntar porque alguém simularia um computador em outro computador, com uma possível perda de desempenho. Aparentemente trata-se de uma tarefa completamente inútil e desnecessária, mas na realidade é uma das soluções mais úteis e que tem se popularizado muito nos últimos anos.

Servidores, como o próprio nome diz, tem a função de prover serviços. São computadores normais, na maioria dos casos com poucas diferenças em relação a um computador doméstico (normalmente para garantir [redundância e tolerância a falhas](http://www.hardware.com.br/livros/servidores-linux/capitulo-hardware-servidores.html)). O problema é que na maior parte do tempo um servidor fica inativo esperando requisições ou até mesmo as recebendo frequentemente mas não utilizando toda a sua capacidade (seja de processamento, memória, rede ou armazenamento). Sendo assim, por que não aproveitar melhor estes recursos, ao mesmo tempo agregando praticidade ao seu gerenciamento e diminuindo os custos?

O que é mais interessante e barato? Comprar um único servidor parrudo com recursos de redundância ou vários servidores mais baratos, menos potentes e mais propensos a falhas? Temos de lembrar que o custo não está envolvendo apenas o preço dos equipamentos, mas também [gastos como o consumo de energia elétrica](http://computerworld.uol.com.br/tecnologia/2011/04/08/virtualizacao-reduz-em-60-custos-de-energia-na-univen/) (o que inclui refrigeração), por exemplo.

Ao optar pela virtualização, você poderia "dividir" este servidor em várias máquinas virtuais, isolando cada uma (um travamento ocasionado por software dentro de uma máquina virtual não afetaria as outras). Além de poder facilmente transplantar o sistema já configurado para outra máquina, simplesmente copiando o arquivo de imagem referente do HD da mesma (no caso de uma falha de hardware na máquina física).

Não é apenas no meio corporativo onde a virtualização faz diferença. Você pode estudar diversos sistemas operacionais diferentes, fazer testes de software de forma rápida segura (como seu programa se comporta em diferentes versões do Windows?) ou simular uma rede inteira no conforto da sua casa com o [VirtualBox](http://www.virtualbox.org/), uma das melhores ferramentas gratuitas de virtualização (além de ser a [mais simples](http://blogs.sun.com/elenilsonvieira/entry/tutorial_usando_o_virtual_box)). Se o seu processador tiver suporte a virtualização (instruções [AMD-V](http://en.wikipedia.org/wiki/X86_virtualization#AMD_virtualization_.28AMD-V.29) ou [Intel VT-x](http://en.wikipedia.org/wiki/X86_virtualization#Intel_virtualization_.28VT-x.29)), você pode até mesmo rodar sistemas 64 bits em um computador com um sistema operacional de 32 bits (graças ao [fiasco do Itanium](http://www.hardware.com.br/livros/hardware/itanium.html)).
