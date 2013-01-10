---
date: 2011-10-07 12:01:23
layout: post
slug: o-firewall-esta-bloqueando-seu-acesso-ao-github
title: O firewall está bloqueando seu acesso ao GitHub?
categories:
- firewall
- github
---

Uma das formas mais simples e seguras de se transmitir dados via internet é utilizando o SSH. Embora seu objetivo primário seja oferecer acesso ao shell de um servidor remoto, o SSH oferece, [dentre as suas mais diversas funcionalidades](http://en.wikipedia.org/wiki/Secure_Shell#Uses), suporte à transferência de arquivos. Como o Git é extremamente versátil quanto à utilização de repositórios remotos, estes podem ser desde um diretório qualquer no seu computador, um servidor de arquivos em uma máquina Windows e até uma pasta compartilhada via Dropbox. A partir disto, nada mais intuitivo do que utilizar o SSH para lidar com repositórios remotos, que é exatamente que o GitHub faz por padrão.

O maior problema é que o SSH, ao menos não sem alterações em suas configurações padrão, não roda em uma porta comumente aberta nas mais diversas configurações de firewall existentes. Se o administrador da rede disponibilizou apenas as portas necessárias para navegação, como 53 (DNS), 80 (HTTP) e 443 (HTTPS), o SSH, residindo humildemente na sua porta 22, ficou de fora. É interessante notar que, em um ambiente com um certo nível de controle, as portas de saída são bloqueadas no firewall de forma que os usuários da rede possam acessar apenas os serviços especificados pelas políticas internas da instituição. Já ouvi relatos de firewalls draconianos onde mesmo as portas 53 (restringindo o acesso à um servidor DNS interno) e 443 (permitindo o acesso apenas à servidores previamente autorizados em uma _whitelist_) são bloqueadas. Vai ver é uma tentativa dos administradores te fazerem sentir como se estivesse acessando a internet atrás do [Grande Firewall da China](http://computerworld.uol.com.br/seguranca/2007/09/13/idgnoticia.2007-09-13.5290686105/).

Pensando neste tipo de situação, o pessoal do GitHub disponibilizou uma solução extremamente simples: todos os repositórios estão disponíveis para serem acessados pela porta 443 no host ssh.github.com. A partir disto, basta apenas configurar seu cliente SSH para utilizar as definições pré-determinadas, de forma que você não tenha que sequer alterar os comandos disponíveis no GitHub (onde você pode simplesmente copiar e colar). Dentro da sua pasta ".ssh" (que fica no diretório "/home/usuario/" no Linux, no "C:\Documents and Settings\usuario\" no Windows XP e no "C:\Users\usuario\" no Windows Vista/7), crie um arquivo chamado "config" (sem extensão) e adicione o seguinte conteúdo:

    Host github.com
        Port 443
        HostName ssh.github.com
        User git
        PreferredAuthentications publickey

Os parâmetros por si só são auto-explicativos, mas vale a pena dissecá-los:

**Linha 1**: Toda vez que o endereço de um host for "github.com", o SSH irá buscar as configurações referentes ao mesmo definidas neste arquivo. Não é necessário que seja necessariamente um domínio válido, pode ser qualquer coisa, já que se trata apenas de um "atalho". Prefiro configurá-lo como se fosse uma URL (como "github.ssl", embora seja preferível a utilização do "github.com", pelo motivo citado anteriormente) para não confundí-lo com os aliases definidos futuramente com o comando "git remote add ...".  
**Linhas 2 e 3**: Estas são as únicas linhas realmente necessárias (além da primeira, claro). Elas definem pra onde o host "github.com" aponta, tanto o novo endereço como a nova porta.  
**Linha 4**: Opcionalmente, podemos definir também o usuário. Desta forma, ao invés de utilizar as URLs no formato "**git@**github.com:usuario/repositorio.git", você poderia se referir às mesmas como "github.com:usuario/repositorio.git". Note que não se trata do seu usuário do GitHub e sim do usuário SSH, que por lá é sempre o "git".  
**Linha 5**: Outra linha opcional. Aqui estamos dizendo ao cliente SSH que o método preferível de autenticação é utilizando chaves criptográficas ao invés de senhas (embora sua chave possa estar protegida por uma passphrase, que funciona como uma senha para a mesma).

Como você pode ver, é um processo extremamente simples, mas que até hoje não vi documentado de forma didática. Adaptei as ideias dos posts "[Using Github Through Draconian Proxies](http://returnbooleantrue.blogspot.com/2009/06/using-github-through-draconian-proxies.html)" e "[how to make github and proxy play nicely with ssh](http://skim.la/2010/02/22/how-to-make-github-and-proxy-play-nicely-with-ssh/)", simplificando-as e removendo os passos desnecessários. Uma observação muito importante (já vi mais uma pessoa cometendo este engano), é que o arquivo tem de ser nomeado "config" e não "config.txt" ou qualquer coisa parecida. Se você não conseguir remover a extensão do mesmo, [desabilite a opção de "ocultar as extensões dos tipos de arquivos conhecidos"](http://windows.microsoft.com/pt-BR/windows-vista/Show-or-hide-file-name-extensions) no Windows Explorer, ou o renomeie [utilizando a linha de comando](http://www.hardware.com.br/dicas/basico-linha-comando.html) do próprio Git Bash. Ainda há a opção de se utilizar [diretamente o HTTPS](https://github.com/blog/642-smart-http-support) (ao invés do SSH na porta dele) no GitHub. O único problema desta abordagem é que a senha tem de ser digitada a cada comando que for lidar com o repositório remoto, já que não há autenticação por chaves via HTTPS.
