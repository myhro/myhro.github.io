---
date: 2014-07-10 15:49
layout: post
title: "Simplificando a utilização do Cron"
categories:
- cron
- linux
---

O Cron é o agendador de tarefas padrão _de facto_ do mundo Unix. No Linux, a versão mais difundida é o Vixie Cron, desenvolvido originalmente por Paul Vixie em 1987 (é incrível perceber como os [utilitários do Unix][unix-utils] são atemporais). Além de poder simplesmente adicionar scripts para serem executados nas pastas `/etc/cron.{hourly,daily,weekly,monthly}`, é possível utilizar um arquivo `crontab`, para obter maior flexibilidade no agendamento de tarefas. Esta abordagem, entretanto, apresenta duas complicações principais:

* A [sintaxe dos horários dos agendamentos][cron-syntax], embora clara, pode ser um pouco críptica para casos mais simples.
* O [ambiente de execução das tarefas][cron-env] é diferente de um shell habitual, sendo muito mais restrito, tornando bastante comum o caso de scripts que funcionam perfeitamente quando testados na linha de comandos, mas que falham quando adicionados ao cron.

O primeiro caso possui um facilitador não muito conhecido, qual só descobri quando me deparei com [este artigo no Linux Journal][linux-journal-cron] no mês passado: é possível utilizar atalhos, tornando a sintaxe muito mais legível:

Atalho    | Significado
--------- | ----------------------------------------------------------
@reboot   | Executar uma vez, na inicialização.
@hourly   | Executar uma vez por hora, equivalente a `"0 * * * *"`.
@daily    | Executar uma vez por dia, equivalente a `"0 0 * * *"`.
@midnight | (O mesmo que `@daily`)
@weekly   | Executar uma vez por semana, equivalente a, `"0 0 * * 0"`.
@monthly  | Executar uma vez por mês, equivalente a `"0 0 1 * *"`.
@yearly   | Executar uma vez por ano, equivalente a `"0 0 1 1 *"`.
@annually | (O mesmo que `@yearly`)

Esta lista pode ser encontrada na [man page do crontab, seção 5][crontab-man].

No segundo caso, o sintoma mais comum é o fato dos comandos simplesmente não funcionarem: uma chamada ao `wget` gera uma mensagem de "_command not found_". Um paleativo é simplesmente substituir os comandos pelo caminho completo do executável, como `/usr/bin/wget`. Mas no caso de scripts mais complexos, isso não apenas fica impraticável, como pode se tornar impossível caso este chame um outro script externo (como no caso de um script em shell chamando outro escrito em Python). A solução definitiva é definir a variável `$PATH` [no próprio crontab][crontab-path], antes das linhas onde os comandos são agendados, como:

    # Edit this file to introduce tasks to be run by cron.
    #
    # Each task to run has to be defined through a single line
    # indicating with different fields when the task will be run
    # and what command to run for the task
    #
    (...)
    #
    # For more information see the manual pages of crontab(5) and cron(8)
    #
    PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
    # m h  dom mon dow   command
    (...)

E deste momento em diante, todos os scripts executados a partir do cron passarão a estar cientes dos caminhos dos executáveis do sistema. Caso necessário, é possível também definir a variável `$SHELL`, para que os scripts sejam executados em outro shell que não o padrão `/bin/sh`.

[cron-env]: http://www-01.ibm.com/support/docview.wss?uid=isg3T1011623
[cron-syntax]: https://en.wikipedia.org/wiki/Cron#Format
[crontab-man]: http://manpages.debian.org/cgi-bin/man.cgi?query=crontab&sektion=5
[crontab-path]: https://stackoverflow.com/questions/2388087/how-to-get-cron-to-call-in-the-correct-paths
[linux-journal-cron]: http://www.linuxjournal.com/content/rclocal-cron-style
[unix-utils]: https://en.wikipedia.org/wiki/List_of_Unix_utilities
