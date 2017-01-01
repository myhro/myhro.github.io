---
date: 2011-04-10 23:14:33
layout: post
title: Python 2 e 3 lado a lado
...

Embora eu esteja cursando nove disciplinas (seriam dez se não tivesse deixado Cálculo Diferencial e Integral I de lado por enquanto), fazendo estágio e participando de dois grupos de estudos (possivelmente três, em breve), tenho estudado Python no meu tempo livre. Na verdade minha relação com o Python se estende por dois ou três anos. O problema é que nunca estudei esta linguagem de programação semanas a fio, pois sempre começava e logo depois parava.

Comecei utilizando o ebook "[Aprenda a Programar](http://www.python.org.br/wiki/DocumentacaoPython?action=AttachFile&do=view&target=Aprenda_a_Programar-Luciano_Ramalho.pdf)" do Luciano Ramalho, disponível no site [PythonBrasil](http://www.python.org.br/wiki) e posso dizer que este não é exatamente um guia muito detalhado (eu utilizava %d ou %f para imprimir variáveis sem saber o porque). Em seguida passei a portar meus pequenos programas escritos em C++ me baseando no pouco que conhecia da sintaxe da linguagem. Recentemente passei a utilizar o guia "[Aprenda Computação com Python](http://www.franciscosouza.com.br/aprendacompy/)" (muito didático, diga-se de passagem), mas tive uma certa dificuldade na parte de Classes e Orientação a Objetos (algo que até então era novo pra mim).

Diante destas dificuldades, passei a procurar vídeo-aulas e me deparei com o curso "[Python 3 Essential Training](http://www.lynda.com/Python-3-tutorials/essential-training/62226-2.html)", da [Lynda.com](http://www.lynda.com/) (eles produzem vídeo-aulas sobre informática desde 2002, quase 10 anos!). O problema era o seguinte: sempre utilizei a versão 2 do Python. Sendo assim, meu objetivo passou a ser traduzir as pequenas diferenças entre as duas versões no decorrer do curso. Mas e quando eu realmente precisasse rodar algum código exclusivo do Python 3? Pensei em utilizar uma máquina virtual só pra isso, mas com certeza deveria haver uma solução mais simples e elegante de utilizar as duas versões lado a lado.

Me deparei então com [esta dica no Stack Overflow](http://stackoverflow.com/questions/341184/can-i-install-python-3-x-and-2-x-on-the-same-computer/436455#436455). É tudo muito simples, basta criar um arquivo "python2.bat" contendo:

    @C:\Python27\python.exe %*

Para o Python 2 (tenho utilizado a versão 2.7.1) e outro "python3.bat" contendo:

    @C:\Python32\python.exe %*

Para o Python 3 (versão 3.2, no caso). Depois disso, basta copiá-lo para a pasta C:\Windows\ ou C:\Windows\System32\ ou qualquer outra pasta que faça parte do seu "Path". Daí em diante você poderá executar seus scripts com "python2 codigo.py" ou "python3 codigo.py", dependendo da necessidade.

Como [deixo o Python 2.7.1 no "Path"](/images/2011/path.png), caso eu abra os arquivos ".py" direto do Windows Explorer ou execute-os sem especificar a versão (como "python arquivo.py", por exemplo), este será aberto com o Python 2. Ao final tenho ambas as versões instaladas, sendo a 2 utilizada como padrão.
