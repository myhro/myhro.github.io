---
date: 2011-07-08 13:45:52
layout: post
title: 'POO Parte 2: Herança e Polimorfismo'
...

Um dos principais objetivos da Programação Orientada à Objetos é facilitar a reutilização de código. Para isto temos duas técnicas básicas que são a Herança e o Polimorfismo. Uma classe pode herdar métodos de outra classe. Se ela apenas os herda e os utiliza sem nenhuma modificação, isto é conhecido como Herança. Se ela os herda e os modifica, ou seja, o mesmo método se comporta de formas diferentes em classes diferentes, isto é conhecido como Polimorfismo.

Podemos ver como isto é facilmente realizado em Python:

{% highlight python %}
class Time():
    def campeonato(self):
        print 'Campeonato Brasileiro de Futebol'
    def nome(self):
        print 'Um time de futebol'
    def tradicao(self):
        print 'Um time decente sempre traz titulos para sua torcida'

class Atletico(Time):
    def nome(self):
        print 'Clube Atletico Mineiro'
    def tradicao(self):
        print 'Nao ganha nada desde 1971'

class Cruzeiro(Time):
    def nome(self):
        print 'Cruzeiro Esporte Clube'

def main():
    cam = Atletico()
    cru = Cruzeiro()
    print 'cam: '
    cam.nome()
    cam.campeonato()
    cam.tradicao()
    print 'cru: '
    cru.nome()
    cru.campeonato()
    cru.tradicao()

if __name__ == "__main__": main()
{% endhighlight %}

Existe uma classe "_Time_", bem genérica, que diz qual campeonato o time participa, assim como seu nome e sua tradição. A classe "_Atletico_" herda o método "_campeonato()_", mas modifica os métodos "_nome()_" e "_tradicao()_". A classe "_Cruzeiro_", por sua vez, modifica apenas o método "_nome()_" e herda os outros dois. Veja como é a saída deste programa:

    cam:
    Clube Atletico Mineiro
    Campeonato Brasileiro de Futebol
    Nao ganha nada desde 1971
    cru:
    Cruzeiro Esporte Clube
    Campeonato Brasileiro de Futebol
    Um time decente sempre traz titulos para sua torcida

O objeto "cam" é uma instância da classe "Atletico" e o "cru" é uma instância da classe "Cruzeiro".

Claro que este é um dos exemplos mais simples possíveis, mas serve perfeitamente para se perceber a utilidade destas técnicas comuns à todas (ou pelo menos quase todas) linguagens orientadas à objetos.

Fonte: [Programação Orientada a Objetos com C++ - Definições](http://www.dca.fee.unicamp.br/cursos/POOCPP/node4.html)
