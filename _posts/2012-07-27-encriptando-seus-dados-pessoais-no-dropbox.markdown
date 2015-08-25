---
date: 2012-07-27 13:57:47
layout: post
title: Encriptando seus dados pessoais no Dropbox
categories:
- backup
- seguranca
---

Por mais cuidadoso que eu seja com meus backups (já perdi dados mais uma vez por conta de backups mal-feitos ou inexistentes), percebi que a abordagem qual estive utilizando nos últimos anos apresentava algumas falhas. A prática consistia basicamente em sincronizar as pastas de documentos entre o meu notebook e o computador-desktop diariamente, realizando backups dos dados mais importantes semanalmente no [Google Docs](https://docs.google.com/) e no [Windows Live Skydrive](https://skydrive.live.com/). O problema estava justamente neste backup semanal.

Como todos os documentos reunidos resultam em alguns GB, o backup semanal corresponde apenas a parcela mais crítica, representando pouco menos de uma centena de MB. Desta forma, fica muito mais fácil realizar o upload destes arquivos, uma vez que além de não dispor de banda larga "de verdade" (algo do [nível da Coréia do Sul](http://www.tecmundo.com.br/internet/17506-por-que-a-coreia-do-sul-tem-a-melhor-internet-do-mundo.htm)), a Mastercabo ainda fez o favor de reduzir a velocidade de upstream pela metade há alguns anos. Isso me lembra [aquela antiga comunidade do Orkut](http://www.orkut.com/Main#Community?cmm=1262932&hl=pt-BR)... Enfim. Parando pra pensar um pouco, percebi que mesmo tendo duas cópias dos meus dados em computadores diferentes, isto não adiantaria nada em caso de uma catástrofe, sendo ela natural (como uma enchente) ou não (como um roubo).

Passei as últimas semanas pensando em qual seria a melhor, ou pelo menos mais simples e eficiente, forma de realizar este backup "completo". Como citei no post sobre [backup de servidores com o Dropbox](http://blog.myhro.info/2011/06/backup-do-seu-servidor-com-o-dropbox/), a alternativa de compactar cada pasta separadamente, encriptá-la e só depois salvá-la na pasta a ser sincronizada, embora seja simples e funcione perfeitamente, não é eficiente. O backup semanal já é realizado desta maneira, mas eu queria algo mais prático e não necessariamente cronológico, já que apenas a última cópia já seria o suficiente. Cheguei a cogitar simplesmente sincronizar estas pastas, com o [SyncToy](http://www.microsoft.com/en-us/download/details.aspx?id=15155) ou [rsync](http://en.wikipedia.org/wiki/Rsync), com uma cópia presente na pasta do Dropbox, mas isto não seria de forma alguma seguro.

O [Dropbox](https://www.dropbox.com/) é muito bacana, muito mesmo. É um daqueles exemplares de software que te fazem feliz porque "simplesmente funcionam". Você move ou copia um arquivo e este se torna disponível automaticamente em diversos computadores ou mesmo a partir do site. Quer compartilhar um arquivo com alguém? Um clique com o botão direito e você copia seu link direto. Com outro clique o destinatário pode baixá-lo do outro lado do mundo, sem qualquer tipo de espera ou limite de velocidade. Isso sem falar na possibilidade de se compartilhar pastas entre um grupo de pessoas. Ah sim, já disse que isso tudo é de graça? Você ainda pode expandir seus 2 GB de espaço inicial realizando as tarefas no site ou indicando o Dropbox para outras pessoas, até um máximo de 16 GB (atualmente, ano passado o máximo eram 8 GB). Por falar nisso, caso você se [cadastre utilizando meu link como referência](http://db.tt/wt0PmPZ2), nós dois ganhamos 500 MB.

Mesmo assim, o Dropbox não é perfeito. Quer dizer, funcionalmente ele se aproxima muito da perfeição, mas no quesito segurança ele pode deixar a desejar em alguns momentos. Existiria então uma forma simples de aliar a praticidade do Dropbox com a segurança dos dados pessoais armazenados no mesmo? Eu poderia criar um volume do [Truecrypt](http://www.truecrypt.org/), mas seria extremamente contra-produtivo pré-alocar o espaço de armazenamento no Dropbox. Fiquei me perguntando se não seria possível encriptar os arquivos on-the-fly, mas individualmente ao invés de salvá-los todos num só "arquivão". Daí lembrei de ter me deparado com [algo parecido há alguns meses no HowToForge](http://www.howtoforge.com/encrypt-your-data-with-encfs-debian-squeeze-ubuntu-11.10). Quando visitei novamente a página, vi que o [EncFS](http://www.arg0.net/encfs/) era exatamente o que estava procurando. O propósito do EncFS é oferecer uma camada por cima do sistema de arquivos já existente, onde todo e qualquer dado é encriptado, com um algoritmo como o [AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) ou [Blowfish](https://en.wikipedia.org/wiki/Blowfish_%28cipher%29), antes de ser gravado em disco.

{% img center /images/2012/yo-dawg-fs.jpg %}

Sua utilização é muito simples, pois você só precisa, basicamente, "montar uma pasta em outra". Parece confuso, mas não é. Imagine duas pastas, uma com os dados encriptados e outra vazia. Ao se montar a pasta com os dados encriptados na pasta que até então estava vazia, estes lá aparecerão desencriptados. E caso a primeira pasta não tenha sido configurada para armazenar os dados encriptados, um assistente o guiará no restante do processo. A partir daí possível é escolher entre dois modos, standard ou paranoia, onde o programa escolherá sozinho os parâmetros de configuração, mas recomendo escolher a opção expert por um único motivo: a criptografia dos nomes dos arquivos.

Configurando manualmente, você pode escolher se quer encriptar ou não o nome dos arquivos. Pode soar desnecessário, uma vez que o importante mesmo é o conteúdo estar encriptado, mas um arquivo com o nome "minha-senha-do-banco.txt" atrai muito mais atenção do que outro com o nome "3T39Hb4uP7be6Mexw5CvDZ6ZCLEz787TabZJTZD-xSc9y0". É claro que você não salvaria a senha do banco em um arquivo-texto, mas lembre-se que você está se protegendo para o caso de outras pessoas obterem acesso aos seus arquivos.

Ao optar por ativar a criptografia dos nomes dos arquivos, é sugerido, por padrão, ativar opção "**Filename Initialization Vector Chaining**", mas eu recomendo fortemente que você não o faça. O que esta opção faz é tornar o nome do arquivo encriptado dependente do caminho onde este se encontra, de forma que dois arquivos de mesmo nome em pastas diferentes tenham nomes encriptados diferentes. Estaria tudo bem, se não fosse o fato de que quando se move um arquivo encriptado para outra pasta, este se torna inútil, não sendo mais possível lê-lo enquanto não o voltar para a pasta correta. Outro efeito colateral é que ao se renomear uma pasta, todos os nomes dos arquivos e subpastas presentes na mesma teriam de ser reencriptados.

Por mais que a intenção seja boa, dificultando um pouco mais a vida de quem tente ler seus arquivos encriptados, esta opção pode trazer muito mais dores de cabeça do que benefícios. Você não poderia, por exemplo, pegar um arquivo isolado de uma pasta qualquer e desencriptá-lo individualmente (e facilmente) em outro computador, a não ser que se recriasse, de forma idêntica, toda a estrutura de pastas onde o mesmo estava armazenado. Isso sem citar que o simples fato de clicar errado e arrastar uma pasta pra dentro da outra (o que acontece muito mais frequentemente do que se imagina) já inutilizaria um diretório inteiro. É importante ressaltar que, em primeiro lugar, você não deveria estar mexendo diretamente nos arquivos encriptados, mas a hipótese não pode ser simplesmente descartada. Além do mais, alguém que te odeia poderia facilmente fazer isto por você.

Veja com é fácil criar sua pasta encriptada com o EncFS (as opções onde você simplesmente aceita os parâmetros padrões foram omitidas):

    [myhro@squeeze:~]$ mkdir crypt plain
    [myhro@squeeze:~]$ ls
    crypt plain
    [myhro@squeeze:~]$ encfs ~/crypt/ ~/plain/
    Creating new encrypted volume.
    Please choose from one of the following options:
     enter "x" for expert configuration mode,
     enter "p" for pre-configured paranoia mode,
     anything else, or an empty line will select standard mode.
    ?> x
    (...)
    Enable filename initialization vector chaining?
    This makes filename encoding dependent on the complete path,
    rather then encoding each path element individually.
    The default here is Yes.
    Any response that does not begin with 'n' will mean Yes: no
    (...)
    Now you will need to enter a password for your filesystem.
    You will need to remember this password, as there is absolutely
    no recovery mechanism. However, the password can be changed
    later using encfsctl.
    New Encfs Password:
    Verify Encfs Password:
    [myhro@squeeze:~]$

Ao fim do processo, sua pasta já deve estar montada. O funcionamento é muito simples: todos os arquivos da pasta não-encriptada (a segunda especificada) são encriptados antes de serem salvos na primeira. Exemplo:

    [myhro@squeeze:~]$ touch plain/README
    [myhro@squeeze:~]$ ls -lha plain/
     total 8.0K
     drwxr-xr-x 2 myhro myhro 4.0K Jul 10 01:56 .
     drwx------ 5 myhro myhro 4.0K Jul 10 01:50 ..
     -rw-r--r-- 1 myhro myhro 0 Jul 10 01:56 README
    [myhro@squeeze:~]$ ls -lha crypt/
     total 12K
     drwxr-xr-x 2 myhro myhro 4.0K Jul 10 01:56 .
     drwx------ 5 myhro myhro 4.0K Jul 10 01:50 ..
     -rw-r--r-- 1 myhro myhro 0 Jul 10 01:56 1a7CvDTFxijsh6jAfW7svR59
     -rw-r--r-- 1 myhro myhro 1.1K Jul 10 01:51 .encfs6.xml

Você pode perceber que na pasta encriptada há um arquivo a mais, o ".encfs6.xml". Nele estão guardadas informações como qual algoritmo criptográfico foi utilizado, o [sal da senha](http://crackstation.net/hashing-security.htm), o número de iterações do [PBKDF2](http://en.wikipedia.org/wiki/PBKDF2), a senha encriptada, etc. Como a ideia é simplesmente pegar esta pasta e colocar no Dropbox, isto pode ser visto como uma falha de segurança, mas, desde que você tenha escolhido uma boa senha ([o que não é tão difícil](http://xkcd.com/936/)), não há com o que se preocupar. Mesmo diante de todas estas informações, o máximo que um atacante poderia fazer é tentar "adivinhar" sua senha na força-bruta, tentando todas as combinações possíveis. Como o PBKDF2 se encarrega em retardar o cálculo do hash da senha em algo em torno de 0,5 (modo padrão) a 3 (modo paranoia) segundos, um atacante não poderia tentar muitas combinações num curto espaço de tempo.

Vale ressaltar que o PBKDF2 se baseia na velocidade do seu processador para determinar quantas iterações são necessárias para retardar o cálculo do hash. Desta forma, é interessante tirar o notebook do modo de economia de energia ou mesmo utilizar um computador mais potente para gerar a chave. Se mesmo assim você se sentir inseguro por deixar tais dados no Dropbox, você pode, por exemplo, colocar lá apenas uma subpasta deste volume encriptado. Só tome muito cuidado para não esquecer a senha ou perder o arquivo de configuração, pois sem qualquer um dos dois não há como reaver seus arquivos.

Utilizei o Linux nos exemplos, mas há um port do EncFs para Windows, o [encfs4win](http://members.ferrara.linux.it/freddy77/encfs.html). Ele funciona exatamente da mesma forma, embora dependa do [Dokan](http://dokan-dev.net/en/download/#dokan) (o [FUSE](http://en.wikipedia.org/wiki/Filesystem_in_Userspace) para Windows, que permite a criação de uma nova camada em cima do sistema de arquivos) e disponibilize uma interface gráfica muito simples, porém funcional, onde é possível criar novos volumes ou montar os já existentes. Embora o próprio autor classifique o port como experimental, talvez por ser um projeto recente, fiz alguns testes com literalmente milhares de arquivos totalizando alguns GB de dados e não encontrei problemas. Recomendo apenas que ao criar uma nova pasta encriptada você o faça em modo texto para escolher melhor as opções.

Por via das dúvidas, minha recomendação final é que você não utilize o EncFS para encriptar diretamente seus arquivos, pelo menos no Windows, onde o software pode não estar maduro. Encripte apenas seus backups e tenha mais de uma cópia dos mesmos em mais de um lugar. Se ainda assim algo der errado, você não guardou todos os ovos na mesma cesta. Além disto, jamais utilize um software criptográfico sem entender seu funcionamento. Você poderia querer arrancar seus cabelos ao tentar recuperar um arquivo e descobrir que isto não é possível por causa de um detalhe como aquele com relação ao encadeamento de nomes.
