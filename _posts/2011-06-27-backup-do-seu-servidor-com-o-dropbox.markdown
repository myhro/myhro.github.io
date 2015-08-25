---
date: 2011-06-27 10:01:57
layout: post
title: Backup do seu servidor com o Dropbox
categories:
- backup
- servidor
---

Minha atual política de backup é bem simples: diariamente faço cópias de arquivos importantes e bancos de dados automaticamente durante a madrugada. Escolher a madrugada para se realizar cópias de segurança é comum pois é bem provável que não estarão alterando os arquivos por ser um horário de pouquíssima atividade (mesmo sendo possível fazer [backup do MySQL sem parar o serviço](http://www.hardware.com.br/dicas/backup-mysql.html)). Todo domingo faço uma cópia destes arquivos, salvando-os em um arquivo maior contendo o backup semanal, também de forma automática. Quando os arquivos vão ficando "velhos" (com mais de dois, três ou seis meses, por exemplo), deleto-os manualmente.

Isto tem funcionado há alguns anos, mas apenas criar cópias dos arquivos salvando-os no mesmo computador ou HD não é o suficiente. Pode ser o bastante para pequenas falhas de software ou lógica, como apagar arquivos errados ou entradas incorretas no banco de dados. Mas e se o seu HD queimar (literalmente, pois pode haver um incêndio)? E se o seu array RAID for comprometido? Para este tipo de catástrofe, salvar cópias dos seus backups em outra localização pode ser muito útil.

Sendo assim, adotei o [Dropbox](http://www.dropbox.com/) para esta finalidade. Mesmo o plano gratuito de 2 GB (podendo-se expandir para até 8 GB caso outras pessoas utilizarem sua indicação para se cadastrarem) pode servir para armazenar muita coisa (ainda há [planos pagos](https://www.dropbox.com/pricing) de 50 ou 100 GB). O problema reside na hora de confiar seus dados (afinal, se você está fazendo backup provavelmente é algo importante) a terceiros. Pode acontecer não apenas de [alguém invadir sua conta](http://meiobit.com/86993/falha-dropbox-quatro-horas-dados-expostos/), como [um funcionário mal intencionado pode bisbilhotar seus arquivos](http://gawker.com/5637234/gcreep-google-engineer-stalked-teens-spied-on-chats). E acredite, se isso aconteceu no Google, porque não aconteceria em outro lugar?

Quanto a confiabilidade, desde que se escolha uma senha segura, você pode criptografar seus arquivos antes de enviá-los. Utilizando um algoritmo decente, como o [AES](http://en.wikipedia.org/wiki/Advanced_Encryption_Standard), você tem a garantia de que [nem mesmo o FBI](http://g1.globo.com/politica/noticia/2010/06/nem-fbi-consegue-decifrar-arquivos-de-daniel-dantas-diz-jornal.html) conseguirá acessar seus dados. O mais interessante disso tudo é que todo o processo é bem simples e você pode automatizá-lo utilizando um shell-script (o Hugo Cisneiros (autor do [The Linux Manual](http://www.devin.com.br/tlm/), no final dos anos 1990/início dos 2000) escreveu [uma introdução bem bacana](http://www.devin.com.br/shell_script/)) agendado com o [cron](http://www.hardware.com.br/dicas/agendando-tarefas-rotinas-cron.html). Instalar o Dropbox no seu servidor (sem utilizar a interface gráfica) [também não é nenhum bicho de sete cabeças](http://wiki.dropbox.com/TipsAndTricks/TextBasedLinuxInstall).

Abaixo há um pequeno script-exemplo com os comandos necessários para se realizar o backup, encriptar o arquivo e depois enviá-lo ao Dropbox (basta colocar na pasta que ela será sincronizada automaticamente). Todos os poucos passos estão devidamente comentados.

{% highlight bash %}
#!/bin/sh

# A senha. Aqui estou usando uma longa sequência de caracteres alfanuméricos.
# Você pode (e deve) melhorá-la utilizando letras maíusculas, pontuação e
# outros símbolos.
SENHA="e8d95a51f3af4a3b134bf6bb680a213a"

# Variável para armazenar a data no formato 2011-06-27 (ano-mês-dia). Desta
# forma você poderá salvar vários arquivos na mesma pasta com nomes 
# diferentes gerados automaticamente, sendo que em ordem alfabética estes
# serão mostrados do mais antigo para o mais novo.
DATA=$(date +%Y-%m-%d)

# Comando para compactar a pasta (poderia ser apenas um arquivo específico,
# como um dump do banco de dados).
tar -zcf home-$DATA.tar.gz /home/usuario

# Comando para encriptar o arquivo gerado.
openssl enc -e -aes256 -in home-$DATA.tar.gz -out home-$DATA.tar.gz.aes -pass pass:$SENHA

# Depois, basta mover o arquivo encriptado para a pasta do Dropbox (ou qualquer
# outra subpasta dentro dela, de forma a organizar melhor os backups).
mv home-$DATA.tar.gz.aes ~/Dropbox/
{% endhighlight %}

O contra, como você deve ter percebido, é que a senha tem de ser armazenada em texto plano (poderia ser em outro arquivo diferente do script, mas ela ainda estaria em sua forma "pura"). Se você precisasse digitá-la toda vez, o backup não seria automático. Mas antes de tudo, você tem de garantir a segurança do seu servidor (até porque se alguém chegar a invadí-lo, os dados lá presentes provavelmente não estarão encriptados). Caso hajam mais usuários cadastrados no sistema (além do root), um [chmod 700](http://www.hardware.com.br/artigos/permissoes-arquivos/) pode ajudar bastante.

Para fazer o processo reverso, desencriptando o arquivo de backup, basta trocar o "-e" por "-d" e inverter os arquivos de entrada e saída. Por exemplo:

    $ openssl enc -d -aes256 -in home-2011-06-27.tar.gz.aes -out home-2011-06-27.tar.gz -pass pass:e8d95a51f3af4a3b134bf6bb680a213a

Fonte: [DistroWatch.com: Tips and Tricks (by Jesse Smith)](http://distrowatch.com/weekly.php?issue=20110516#tips)
