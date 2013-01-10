---
date: 2011-07-04 12:32:13
layout: post
slug: poo-parte-1-classes-e-objetos
title: 'POO Parte 1: Classes e Objetos'
categories:
- poo
- python
---

O paradigma da Programação Orientada à Objetos não é nenhum bicho de sete cabeças. Até mesmo para quem cursou um único semestre de um curso de computação, presumindo-se que tenha aprendido funções e registros (coleções de variáveis), apenas novos estilos e técnicas são apresentados. Primeiramente, vamos analisar a estruturação de um programa realmente simples (mais ainda por ter sido escrito em Python), que apenas adiciona pessoas em algo parecido com uma lista encadeada (só que sem a necessidade do encadeamento). Você pode perceber que não há tratamento de exceções e a única verificação feita é se a lista está vazia. É realmente apenas um exemplo.

``` python
class Lista():
    def __init__(self):
        self.lista = []
    def libera(self):
        del self.lista[:]
    def imprime(self):
        if len(self.lista) == 0:
            print 'Lista vazia.'
            return
        for pessoa in self.lista:
            print pessoa
    def insere(self, nome):
        self.lista.append(nome)
    def retira(self, nome):
        print 'Removido:', nome
        self.lista.remove(nome)

def main():
    inventario = Lista()
    inventario.insere('Tiago')
    inventario.insere('Dunha')
    inventario.insere('Xunda')
    inventario.imprime()
    inventario.retira('Dunha')
    inventario.imprime()
    inventario.libera()
    inventario.imprime()

if __name__ == "__main__": main()
```

Saída do programa:

    Tiago
    Dunha
    Xunda
    Removido: Dunha
    Tiago
    Xunda
    Lista vazia.

A classe "_Lista_" tem um construtor, a função "___init__()_", que aqui chamaremos de método (um método nada mais é do que uma função de uma classe). O construtor tem como propósito atribuir variáveis e seus possíveis valores ao objeto instanciado. Na função "_main()_", "_inventario_" é um objeto instanciado da classe "_Lista_" que por sua vez tem uma variável "_lista_", que por enquanto nada mais é do que uma lista vazia. Se lhe pareceu confuso, tenha em mente que em Python não existem vetores e sim listas (ou tuplas, que são listas imutáveis). Um matriz em Python seria uma lista de listas. A partir de agora podemos usar os métodos do objeto "_inventario_".

O método "_libera()_" apenas remove todo o conteúdo da lista (sem remover a lista em si). O método "_imprime()_" percorre a lista (se esta não estiver vazia) e imprime cada um dos seus elementos. No método "_insere()_" que as coisas começam a ficar interessantes. Em Python tudo é um objeto (no Python 3.x até mesmo tipos primitivos como int e float são objetos!). Se uma lista é um objeto, ela provavelmente tem métodos associados à ela. O "_append()_" é um deles. O que ele faz é inserir o argumento passado ao final da lista, sendo que este pode ser uma variável ou um objeto (até mesmo outra lista). O método "_retira()_", por sua vez, chama o método "_remove()_" da lista, encontrando e removendo o elemento passado como argumento.

Se você se perguntou o porque do "_self_" presente em todos os métodos, a explicação é bem simples. O "_self_" se refere ao objeto em questão, qual fez a chamada de determinado método (podem existir infinitos objetos instanciados de uma mesma classe) e é sempre o primeiro argumento passado automaticamente. Você não precisa se preocupar com ele ao chamar um método, apenas ao referenciá-lo dentro da classe. Desta forma, cada vez que você se refere ao objeto ou alguma de suas variáveis, se utiliza "_self_" ou "_self.variável_", respectivamente. Na verdade você não precisa nem mesmo usar o nome "_self_" (poderia ser qualquer outra coisa), mas ao fazer isso muitos programadores Python no mundo ficariam chateados.

O "Capítulo 45: [Is-A, Has-A, Objects, and Classes](http://learnpythonthehardway.org/book/ex45.html)" do livro "[Learn Python The Hard Way](http://learnpythonthehardway.org/)" (do [Zed Shaw](http://zedshaw.com/)) explica o conceito de objetos e classes utilizando coisas do mundo real (peixes e salmões) de uma forma bem didática. Acredito que o "maior problema" com POO é justamente entender sua estruturação na linguagem que você utiliza, já que as técnicas são basicamente as mesmas (instanciação, construção, herança, polimorfismo, etc). Em Python você pode perceber que isto não chega a ser empecilho algum.

A simplicidade do Python muitas vezes se dá devido a sua orientação à objetos. Praticamente tudo de básico (como ordenar uma lista, converter uma string minúscula para maiúscula, etc) que você precise fazer já existe implementado em alguma classe. Você pode ler mais sobre [estruturas de dados em Python](http://docs.python.org/tutorial/datastructures.html) e [sua biblioteca padrão](http://docs.python.org/library/) na [documentação oficial](http://docs.python.org/). Recomendo uma atenção especial ao [tipo dicionário](http://diveintopython.org/getting_to_know_python/dictionaries.html), que é algo realmente diferente e útil.
