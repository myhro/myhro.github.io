---
date: 2015-08-25 20:58:43
layout: post
title: "Myhro Blog agora via Jekyll"
categories:
- jekyll
- octopress
---

Há pouco mais de dois anos e meio, em Janeiro de 2013, o Myhro Blog [havia sido migrado][post-word-octo] do [Wordpress][wordpress] para o [Octopress][octopress]. Fiquei bastante satisfeito com a migração e empolgado com a ideia de não mais depender de um banco de dados para armazenar todas as postagens, utilizando [Git][post-git] para versionar o conteúdo. Com a própria mudança no processo, ficou muito mais natural escrever e publicar tudo a partir do terminal, sem precisar acessar uma interface web para isto. Entretanto, paraiva sobre mim uma preocupação: o Octopress era uma bomba-relógio.

Que fique claro que não irei de forma alguma falar mal do Octopress, que me serviu tão bem por tanto tempo. Serei eternamente grato ao [Brandon Mathis][imathis] por tê-lo criado. O problema é que hoje o Octopress se encontra em um estado conhecido como [erosão (ou apodrecimento) de software][software-rot]. A última versão estável (2.0) foi [lançada em Julho de 2011][octopress-20] e embora a nova versão (3.0) esteja sendo desenvolvida, não sabemos quando será lançada. A [última notícia][octopress-30] a respeito da mesma é de Janeiro deste ano.

O maior problema em tentar utilizar uma versão de um software com mais de 4 anos de idade e tantas dependências é simples: não é mais possível utilizá-la. Mesmo reinstalando [a gem][gemfile] do Octopress, com todas as suas bibliotecas em suas devidas versões (graças ao [Gemfile.lock][gemfile-lock]), apareciam erros que impediam o blog de ser "compilado" (processo de gerar as páginas HTML a partir dos documentos escritos em [Markdown][github-markdown]). Isto acontece, por exemplo, por mudanças externas, como o fato das bibliotecas instaladas no sistema quais as gems dependem para serem compiladas estarem em versões diferentes de quando estas foram desenvolvidas.

A situação chegou a um ponto onde eu só podia publicar novos posts no blog ou modificar qualquer configuração do Octopress no meu notebook. Qualquer outro computador, seja minha estação de trabalho no escritório ou o servidor remoto que utilizo para desenvolvimento, não servia para este propósito. A gota d'água foi quando precisei alterar as configurações do plugin de pesquisa no Google (que estava gerando erros de HTTPS por submeter conteúdo de forma não-segura) e não consegui.

Deste momento em diante tive de decidir o que fazer, se tentaria utilizar a versão 3.0 ainda não lançada ou se abandonaria o Octopress em busca de outra solução. Ao perceber que a nova versão havia se tornado um _wrapper_ ainda mais fino em torno do [Jekyll][jekyll], mas com mais problemas por se tratar de uma versão em desenvolvimento, não tive dúvidas e migrei logo para este último. Há de se ressaltar que algumas funcionalidades foram perdidas (e talvez ainda consiga restaurá-las com um pouquinho de tempo e paciência) no processo:

* A barra "_recent posts_", no canto superior direito, não existe mais. Isto não é uma perda tão grande, já que esta basicamente me servia para notar quais são os cinco posts exibidos na página principal;
* As "categorias" ainda existem, mas não há as páginas de listagem de cada uma, segregando o [arquivo de posts antigos][archives] em partes menores;
* Os links entre posts também foram perdidos. É bacana a ideia de navegar entre "anterior" e "próximo" a partir de um post, mas ainda não verifiquei como resolver isto.

A parte de "unir o útil ao agradável" fica por conta do [GitHub Pages][gh-pages]. Já o estava utilizando desde Junho de 2014, quando percebi que o esforço em manter um servidor para hospedar um site estático não valia a pena. Acontece que o deploy do Octopress por lá é meio que uma gambiarra. Os posts são gerados em uma pasta não commitada do repositório, mas que na verdade é outro repositório, e só então enviados para o GitHub. Com o Jekyll não preciso me preocupar com isto, visto que o GitHub Pages [tem suporte nativo][gh-pages-jekyll] à plataforma.

[archives]: /archives/
[gemfile-lock]: http://bundler.io/v1.10/rationale.html#checking-your-code-into-version-control
[gemfile]: http://bundler.io/v1.10/rationale.html#bundlers-purpose-and-rationale
[gh-pages-jekyll]: https://help.github.com/articles/using-jekyll-with-pages/
[gh-pages]: https://pages.github.com/
[github-markdown]: https://help.github.com/articles/github-flavored-markdown/
[imathis]: https://github.com/imathis
[jekyll]: http://jekyllrb.com/
[octopress-20]: http://octopress.org/2011/07/23/octopress-20-surfaces/
[octopress-30]: http://octopress.org/2015/01/15/octopress-3.0-is-coming/
[octopress]: http://octopress.org/
[post-git]: /2011/08/git-para-principiantes/
[post-word-octo]: /2013/01/myhro-blog-agora-via-octopress/
[software-rot]: https://en.wikipedia.org/wiki/Software_rot
[wordpress]: https://wordpress.org/
