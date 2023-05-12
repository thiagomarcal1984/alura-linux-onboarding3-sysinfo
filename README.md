# Variáveis e scripts

O comando `env` lista as variáveis de ambiente.

Para imprimir uma variável de ambiente de nome VENV, use o comando:
```bash
echo $VENV
```
Para definir um valor na variável VENV com o valor XPTO, use o comando:
```bash
VENV=XPTO
echo $VENV
```

Para criar um arquivo de script chamado backup.sh, use o comando `vi backup.sh` e insira o código do script:
```bash
#!/bin/bash
# O texto !/bin/bash serve para indicar qual shell será usado para rodar o script.
echo "Digite o diretório de backup:"
read diretorio_bkp # Pede para o usuário informar um valor para a variável diretorio_bkp

cp -rv $diretorio_bkp ~/backup # Cópia recursiva e verbosa.
echo ""
echo "Backup Concluído!"
```

# Configurando e executando scripts
Comandos prévios para possibilitar o funcionamento do script `backup.sh`:
```bash
thiago@thiago-pc:~/labs/scripts$ cd
thiago@thiago-pc:~$ mkdir backup
thiago@thiago-pc:~$ ls
00-installer-config.yaml  backup  labs  teste.arq
```

Configure permissão para execução do script `backup.sh`:
```bash
# Sem permissão de execução.
thiago@thiago-pc:~$ ls -l ~/labs/scripts/
total 4
-rw-rw-r-- 1 thiago thiago 324 mai  9 15:09 backup.sh

# Configurando a permissão para execução. 
# O parâmetro `u` corresponde ao dono do arquivo.
# O parâmetro `+x` concede a permissão para execução
chmod u+x ~/labs/scripts/backup.sh

# Sem permissão de execução.
# Agora o dono tem a permissão, repare no `x` no 4o caractere:
thiago@thiago-pc:~$ ls -l ~/labs/scripts/
total 4
-rwxrw-r-- 1 thiago thiago 324 mai  9 15:09 backup.sh
```

Execute o script:
```bash
# Vá até o caminho onde está o script...
thiago@thiago-pc:~/labs/scripts$ pwd
/home/thiago/labs/scripts
# ... e execute o comando backup.sh (o `./` indica o caminho atual, o pwd):
thiago@thiago-pc:~/labs/scripts$ ./backup.sh
```

Saída da execução do script:
```bash
thiago@thiago-pc:~/labs/scripts$ ./backup.sh
Digite o diretório de backup:
# Aqui foi necessário usar o path completo, senão o script não funciona.
/usr/bin

'/usr/bin/timedatectl' -> '/home/thiago/backup/bin/timedatectl'
...
'/usr/bin/systemd-umount' -> '/home/thiago/backup/bin/systemd-umount'

Backup Concluído!
thiago@thiago-pc:~/labs/scripts$
```

O arquivo `~/.profile` executa alguns comandos que modificam o path do sistema operacional:
```bash
# Resto do arquivo
# set PATH so it includes user's private bin if it exists
# Inclui o diretório `bin` do usuário, se existir:
if [ -d "$HOME/bin" ] ; then
    PATH="$HOME/bin:$PATH"
fi# Resto do arquivo
```

Assim, vamos criar a pasta `bin` dentro da pasta do usuário e tentar rodar o script sem precisar informar o path completo do script:
```bash 
thiago@thiago-pc:~/labs/scripts$ mkdir ~/bin
thiago@thiago-pc:~/labs/scripts$ ls
backup.sh
thiago@thiago-pc:~/labs/scripts$ ls ~
00-installer-config.yaml  backup  bin  labs  teste.arq
thiago@thiago-pc:~/labs/scripts$ cp ./backup.sh ~/bin
thiago@thiago-pc:~/labs/scripts$
```
> Para que o script fique disponível, é necessário repetir o login (faça logoff e login em seguida).
```bash
# Exibindo a variável PATH. Repare que o path privado foi inserido (/home/thiago/bin):
thiago@thiago-pc:/$ echo $PATH
/home/thiago/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:/snap/bin
# Mudança para o diretório raiz, longe de ~/bin:
thiago@thiago-pc:~$ cd /
# Executando o script backup.sh:
thiago@thiago-pc:/$ backup.sh
Digite o diretório de backup:
# Cancelando a execução com Ctrl+C:
^C
thiago@thiago-pc:/$
```

# Obtendo informações da rede
<table>
    <tr>
        <th>Comando</th>
        <th>Saída</th>
    </tr>
    <tr>
        <td><code>ip addr</code></td>
        <td><pre>1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host
       valid_lft forever preferred_lft forever
2: enp0s3: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    link/ether 08:00:27:fe:8b:a7 brd ff:ff:ff:ff:ff:ff
    inet 192.168.18.254/24 metric 100 brd 192.168.18.255 scope global dynamic enp0s3
       valid_lft 1451sec preferred_lft 1451sec
    inet6 2804:2488:5084:2270:a00:27ff:fefe:8ba7/64 scope global dynamic mngtmpaddr noprefixroute
       valid_lft 259054sec preferred_lft 172654sec
    inet6 fe80::a00:27ff:fefe:8ba7/64 scope link
       valid_lft forever preferred_lft forever</pre></td>
    </tr>
    <tr>
        <td><code>hostname -I</code></td>
        <td><pre>192.168.18.254 2804:2488:5084:2270:a00:27ff:fefe:8ba7</pre></td>
    </tr>
    <tr>
        <td><code>ip route</code></td>
        <td><pre>default via 192.168.18.1 dev enp0s3 proto dhcp src 192.168.18.254 metric 100
192.168.18.0/24 dev enp0s3 proto kernel scope link src 192.168.18.254 metric 100
192.168.18.1 dev enp0s3 proto dhcp scope link src 192.168.18.254 metric 100</pre></td>
    </tr>
</table>

## O arquivo /etc/resolv.conf
O arquivo `/etc/resolv.conf` contém a lista de servidores de nome (DNS) usados no computador:
```bash
thiago@thiago-pc:/$ cat /etc/resolv.conf
# This is /run/systemd/resolve/stub-resolv.conf managed by man:systemd-resolved(8).
# Do not edit.
#
# This file might be symlinked as /etc/resolv.conf. If you're looking at
# /etc/resolv.conf and seeing this text, you have followed the symlink.
#
# This is a dynamic resolv.conf file for connecting local clients to the
# internal DNS stub resolver of systemd-resolved. This file lists all
# configured search domains.
#
# Run "resolvectl status" to see details about the uplink DNS servers
# currently in use.
#
# Third party programs should typically not access this file directly, but only
# through the symlink at /etc/resolv.conf. To manage man:resolv.conf(5) in a
# different way, replace this symlink by a static file or a different symlink.
#
# See man:systemd-resolved.service(8) for details about the supported modes of
# operation for /etc/resolv.conf.

nameserver 127.0.0.53
options edns0 trust-ad
search .
thiago@thiago-pc:/$
```
O número IP 127.0.0.53 é um servidor local criado pelo Ubuntu para resolver nomes.

## O arquivo /etc/hosts
O arquivo `/etc/hosts` associa um nome a um IP. A boa prática é usar o servidor DNS, não a edição do arquivo `/etc/hosts`.

Ao modficar o arquivo `/etc/hosts` podemos associar um nome a um IP:
```bash
127.0.0.1 localhost
127.0.1.1 thiago-pc

# The following lines are desirable for IPv6 capable hosts
::1     ip6-localhost ip6-loopback
fe00::0 ip6-localnet
ff00::0 ip6-mcastprefix
ff02::1 ip6-allnodes
ff02::2 ip6-allrouters

# Note a linha abaixo, foi inserida manualmente.
1.2.3.4 teste
```

Agora, a saída ao realizar o ping no hostname chamado `teste`:
```bash
thiago@thiago-pc:~$ ping teste
PING teste (1.2.3.4) 56(84) bytes of data.
^C
--- teste ping statistics ---
4 packets transmitted, 0 received, 100% packet loss, time 3154ms
```

# Informações de memória e CPU
## Exibindo as configurações de hardware com o lshw
O comando `sudo lshw` exibe as configurações de hardware do computador. 
> O `sudo` é necessário no comando.

## Exibindo as configurações de memória por meio do arquivo /proc/meminfo
O arquivo `/proc/meminfo` exibe as informações sobre memória RAM do computador:
```bash
thiago@thiago-pc:~$ cat /proc/meminfo
MemTotal:        2023204 kB
MemFree:         1503776 kB
MemAvailable:    1675892 kB
Buffers:           19344 kB
Cached:           275160 kB
SwapCached:            0 kB
Active:           117468 kB
Inactive:         239620 kB
Active(anon):       1044 kB
Inactive(anon):    66880 kB
Active(file):     116424 kB
Inactive(file):   172740 kB
Unevictable:       27876 kB
Mlocked:           27876 kB
SwapTotal:       2097148 kB
SwapFree:        2097148 kB
Dirty:                 8 kB
Writeback:             0 kB
AnonPages:         90652 kB
Mapped:            78868 kB
Shmem:              1100 kB
KReclaimable:      28440 kB
Slab:              61264 kB
SReclaimable:      28440 kB
SUnreclaim:        32824 kB
KernelStack:        2272 kB
PageTables:         2004 kB
NFS_Unstable:          0 kB
Bounce:                0 kB
WritebackTmp:          0 kB
CommitLimit:     3108748 kB
Committed_AS:     479888 kB
VmallocTotal:   34359738367 kB
VmallocUsed:       31448 kB
VmallocChunk:          0 kB
Percpu:             1168 kB
HardwareCorrupted:     0 kB
AnonHugePages:         0 kB
ShmemHugePages:        0 kB
ShmemPmdMapped:        0 kB
FileHugePages:         0 kB
FilePmdMapped:         0 kB
HugePages_Total:       0
HugePages_Free:        0
HugePages_Rsvd:        0
HugePages_Surp:        0
Hugepagesize:       2048 kB
Hugetlb:               0 kB
DirectMap4k:      165824 kB
DirectMap2M:     1931264 kB
```
## O comando free
O comando `free` exibe um resumo da memória, a partir dos dados armazenados no arquivo /proc/meminfo:

```bash
thiago@thiago-pc:~$ free
               total        used        free      shared  buff/cache   available
Mem:         2023204      196484     1503776        1100      322944     1675900
Swap:        2097148           0     2097148
```

## Detalhes da CPU no arquivo /proc/cpuinfo
O arquivo `/proc/cpuinfo` detalha informações da CPU. 

Exemplo para descobrir quantos processadores existem no computador:
```bash
thiago@thiago-pc:~$ thiago@thiago-pc:~$ cat /proc/cpuinfo | grep processor
processor       : 0
processor       : 1
```
Neste exemplo, temos dois núcleos de processamento na VM (ids 0 e 1).

Outro exemplo, mesma pergunta, mas redirecionando para o comando `wc`:
```bash
thiago@thiago-pc:~$ cat /proc/cpuinfo | grep processor | wc -l
2
```

# Dispositivos de blocos (Discos)
As informações dos dispositivos (devices) estão armazenadas no diretório `/dev`.

Quando usamos o comando `ls /dev -l`, nós listamos o diretório `/dev` de forma longa. Note que o primeiro caracter de cada arquivo da pasta `/dev` varia para c (character), b (block) etc.

Exemplo: para visualizarmos o primeiro HD Sata disponível no computador e suas partições, usamos o comando:

```bash
thiago@thiago-pc:~$ ls /dev/ -l | grep sda
brw-rw----  1 root   disk      8,   0 mai  9 20:49 sda
brw-rw----  1 root   disk      8,   1 mai  9 20:49 sda1
brw-rw----  1 root   disk      8,   2 mai  9 20:49 sda2
brw-rw----  1 root   disk      8,   3 mai  9 20:49 sda3
```
> Note que, depois de `sd`, a letra `a` corresponde à letra sequencial do disco, enquanto os números (1, 2, 3) representam as partições existentes. Se quisermos visualizar um segundo HD, executamos o comando `ls /dev -l sdb` e assim sucessivamente.

## O comando df (disk free)
O comando df -h exibe informações de disco (o parâmetro `-h` serve para mostrar informações legíveis para humanos):
```bash
thiago@thiago-pc:~$ df -h
Filesystem                         Size  Used Avail Use% Mounted on
tmpfs                              198M  1,1M  197M   1% /run
/dev/mapper/ubuntu--vg-ubuntu--lv   12G  5,2G  5,6G  49% /
tmpfs                              988M     0  988M   0% /dev/shm
tmpfs                              5,0M     0  5,0M   0% /run/lock
/dev/sda2                          2,0G  130M  1,7G   8% /boot
tmpfs                              198M  4,0K  198M   1% /run/user/1000
```
# Principais arquivos de log do sistema
Os logs geralmente ficam guardados no diretório `/var/log`.

## O comando dmesg
O comando `dmesg` mostra informações de inicialização do sistema (as mesmas informações que aparecem durante o boot). 

O comando a seguir confirma se o HD sda foi montado:
```
thiago@thiago-pc:/var/log$ sudo dmesg | grep sda
[    5.576852] sd 2:0:0:0: [sda] 52428800 512-byte logical blocks: (26.8 GB/25.0 GiB)
[    5.581159] sd 2:0:0:0: [sda] Write Protect is off
[    5.583710] sd 2:0:0:0: [sda] Mode Sense: 00 3a 00 00
[    5.586883] sd 2:0:0:0: [sda] Write cache: enabled, read cache: enabled, doesn't support DPO or FUA
[    5.725931]  sda: sda1 sda2 sda3
[    5.818590] sd 2:0:0:0: [sda] Attached SCSI disk
[   11.018847] EXT4-fs (sda2): mounted filesystem with ordered data mode. Opts: (null). Quota mode: none.
```
## O arquivo /var/log/syslog
O arquivo `/var/log/syslog` (no amibente Red Hat o arquivo se chama `/var/log/messages`) mostra o log das saídas associadas ao kernel do Linux.

Exemplo de comando para visualizar as tarefas agendadas no Linux (busca por cron):
```bash
thiago@thiago-pc:/var/log$ cat syslog | grep -i CRON
Apr 20 16:35:06 thiago-pc cron[664]: (CRON) INFO (pidfile fd = 3)
Apr 20 16:35:06 thiago-pc cron[664]: (CRON) INFO (Running @reboot jobs)
Apr 20 18:32:06 thiago-pc cron[649]: (CRON) INFO (pidfile fd = 3)
Apr 20 18:32:06 thiago-pc cron[649]: (CRON) INFO (Running @reboot jobs)
Apr 20 19:17:01 thiago-pc CRON[1723]: (root) CMD (   cd / && run-parts --report /etc/cron.hourly)
Apr 20 20:17:01 thiago-pc CRON[2022]: (root) CMD (   cd / && run-parts --report /etc/cron.hourly)
Apr 20 21:17:01 thiago-pc CRON[2282]: (root) CMD (   cd / && run-parts --report /etc/cron.hourly)
Apr 21 12:17:02 thiago-pc CRON[2555]: (root) CMD (   cd / && run-parts --report /etc/cron.hourly)
Apr 21 15:17:01 thiago-pc CRON[2878]: (root) CMD (   cd / && run-parts --report /etc/cron.hourly)
Apr 21 16:17:01 thiago-pc CRON[3039]: (root) CMD (   cd / && run-parts --report /etc/cron.hourly)
Apr 21 17:17:01 thiago-pc CRON[3331]: (root) CMD (   cd / && run-parts --report /etc/cron.hourly)
grep: (standard input): binary file matches
```
# Visualizando os logs em tempo real

## Log de autenticação (/var/log/auth.log)
O arquivo `/var/log/auth.log` contém o log de autenticação no Linux.

Comando para exibir histórico de sessões SSH :
```bash
thiago@thiago-pc:/var/log$ strings auth.log | grep -i ssh
Apr 20 16:35:08 thiago-pc sshd[783]: Server listening on 0.0.0.0 port 22.
Apr 20 16:35:08 thiago-pc sshd[783]: Server listening on :: port 22.
Apr 20 16:35:21 thiago-pc sshd[783]: Received signal 15; terminating.
...
May  9 15:38:30 thiago-pc sshd[5220]: pam_unix(sshd:session): session opened for user thiago(uid=1000) by (uid=0)
May  9 18:30:57 thiago-pc sshd[708]: Server listening on 0.0.0.0 port 22.
May  9 18:30:57 thiago-pc sshd[708]: Server listening on :: port 22.
May  9 20:52:03 thiago-pc sshd[718]: Server listening on 0.0.0.0 port 22.
May  9 20:52:03 thiago-pc sshd[718]: Server listening on :: port 22.
May  9 20:54:52 thiago-pc sshd[1033]: Connection reset by 172.21.128.1 port 53701 [preauth]
May  9 20:55:00 thiago-pc sshd[1035]: Accepted password for thiago from 172.21.128.1 port 53702 ssh2
May  9 20:55:00 thiago-pc sshd[1035]: pam_unix(sshd:session): session opened for user thiago(uid=1000) by (uid=0)
thiago@thiago-pc:/var/log$
```
> Perceba que o comando usado foi `strings`, não `cat`. Isso foi necessário porque algumas vezes o arquivo `auth.log` é interpretado como binário, o que impede a concatenação com `cat`. Exemplo:
> ```
> thiago@thiago-pc:/var/log$ cat auth.log | grep -i ssh
> grep: (standard input): binary file matches
> thiago@thiago-pc:/var/log$
> ```

Buscando as 6 últimas saídas referentes a SSH (note que há um registro de tentativa falha de autenticação do usuário `joao`):
```
thiago@thiago-pc:/var/log$ strings auth.log | grep -i ssh | tail -6
May  9 20:55:00 thiago-pc sshd[1035]: pam_unix(sshd:session): session opened for user thiago(uid=1000) by (uid=0)
May  9 22:53:33 thiago-pc sshd[1407]: Invalid user joao from 172.21.128.1 port 54304
May  9 22:53:35 thiago-pc sshd[1407]: pam_unix(sshd:auth): check pass; user unknown
May  9 22:53:35 thiago-pc sshd[1407]: pam_unix(sshd:auth): authentication failure; logname= uid=0 euid=0 tty=ssh ruser= rhost=172.21.128.1
May  9 22:53:37 thiago-pc sshd[1407]: Failed password for invalid user joao from 172.21.128.1 port 54304 ssh2
May  9 22:53:40 thiago-pc sshd[1407]: Connection reset by invalid user joao 172.21.128.1 port 54304 [preauth]
thiago@thiago-pc:/var/log$
```
## Exibição de conteúdo de arquivo em realtime com o comando `tail -f`
O comando `tail -f` mostra o conteúdo do arquivo na tela, e qualquer atualização sobre ele aparece em tempo real.

No exemplo abaixo, o terminal exibe duas tentativas de autenticação falhas com os usuários `zina` e `blaha`:
```bash
thiago@thiago-pc:/var/log$ tail -f auth.log
May  9 22:30:01 thiago-pc sudo: pam_unix(sudo:session): session opened for user root(uid=0) by thiago(uid=1000)
May  9 22:30:01 thiago-pc sudo: pam_unix(sudo:session): session closed for user root
May  9 22:30:16 thiago-pc sudo:   thiago : TTY=pts/0 ; PWD=/var/log ; USER=root ; COMMAND=/usr/bin/dmesg
May  9 22:30:16 thiago-pc sudo: pam_unix(sudo:session): session opened for user root(uid=0) by thiago(uid=1000)
May  9 22:30:16 thiago-pc sudo: pam_unix(sudo:session): session closed for user root
May  9 22:53:33 thiago-pc sshd[1407]: Invalid user joao from 172.21.128.1 port 54304
May  9 22:53:35 thiago-pc sshd[1407]: pam_unix(sshd:auth): check pass; user unknown
May  9 22:53:35 thiago-pc sshd[1407]: pam_unix(sshd:auth): authentication failure; logname= uid=0 euid=0 tty=ssh ruser= rhost=172.21.128.1
May  9 22:53:37 thiago-pc sshd[1407]: Failed password for invalid user joao from 172.21.128.1 port 54304 ssh2
May  9 22:53:40 thiago-pc sshd[1407]: Connection reset by invalid user joao 172.21.128.1 port 54304 [preauth]
May  9 23:00:17 thiago-pc sshd[1443]: Invalid user zina from 172.21.128.1 port 54408
May  9 23:00:39 thiago-pc sshd[1443]: Connection reset by invalid user zina 172.21.128.1 port 54408 [preauth]
May  9 23:00:52 thiago-pc sshd[1445]: Invalid user blaha from 172.21.128.1 port 54409
May  9 23:00:54 thiago-pc sshd[1445]: Connection reset by invalid user blaha 172.21.128.1 port 54409 [preauth]
May  9 23:03:58 thiago-pc sshd[1449]: pam_unix(sshd:auth): authentication failure; logname= uid=0 euid=0 tty=ssh ruser= rhost=172.21.128.1  user=thiago
May  9 23:04:00 thiago-pc sshd[1449]: Failed password for thiago from 172.21.128.1 port 54434 ssh2
May  9 23:04:02 thiago-pc sshd[1449]: Connection reset by authenticating user thiago 172.21.128.1 port 54434 [preauth]
```
> Note que a saída tem 17 linhas, mas o comando tail por padrão exibe as 10 últimas linhas. As 4 linhas a mais são resultado das duas tentativas de login com usuários inexistentes, e as 3 últimas linhas se referem a uma senha inválida.

## O arquivo /etc/logrotate.conf
O arquivo `/etc/logrotate.conf` contém configurações para rotações de logs.
```bash
thiago@thiago-pc:/var/log$ cat /etc/logrotate.conf
# see "man logrotate" for details

# global options do not affect preceding include directives

# rotate log files weekly
weekly

# use the adm group by default, since this is the owning group
# of /var/log/syslog.
su root adm

# keep 4 weeks worth of backlogs
rotate 4

# create new (empty) log files after rotating old ones
create

# use date as a suffix of the rotated file
#dateext

# uncomment this if you want your log files compressed
#compress

# packages drop log rotation information into this directory
include /etc/logrotate.d

# system-specific logs may also be configured here.
```
> Note que o arquivo sugere a leitura do manual do comando `logrotate`, o qual pode modificar essas configurações.

# Listando processos com o PS e o Top
O comando `ps` (Process Status ou Process Snapshots) mostra informações sobre os processos em execução no Linux.

Alguns parâmetros do comando `ps`:
|Parâmetro|Função|
|---|---|
|`-A` ou `-e`|Seleciona todos os processos.|
|`-f`|Exibe a saída em formato de lista completa.|

Exemplo de saída completa do comando `ps -ef | head`:
```bash
thiago@thiago-pc:/var/log$ ps -ef | head
UID          PID    PPID  C STIME TTY          TIME CMD
root           1       0  0 21:20 ?        00:00:03 /sbin/init
root           2       0  0 21:20 ?        00:00:00 [kthreadd]
root           3       2  0 21:20 ?        00:00:00 [rcu_gp]
root           4       2  0 21:20 ?        00:00:00 [rcu_par_gp]
root           5       2  0 21:20 ?        00:00:00 [slub_flushwq]
root           6       2  0 21:20 ?        00:00:00 [netns]
root           8       2  0 21:20 ?        00:00:00 [kworker/0:0H-events_highpri]
root          10       2  0 21:20 ?        00:00:00 [mm_percpu_wq]
root          11       2  0 21:20 ?        00:00:00 [rcu_tasks_rude_]
```
Os campos exibidos com o `ps -ef`:
|Campo|Função|
|---|---|
|UID|Nome do usuário que é o dono do processo.|
|PID|Identificação do Processo.|
|PPID|Parent PID, processo pai.|
|CMD|Comando executado.|

Exemplo de saída do comando `ps aux | head`:
```bash
thiago@thiago-pc:/var/log$ ps aux | head
USER         PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND
root           1  0.0  0.5 100788 11660 ?        Ss   21:20   0:03 /sbin/init
root           2  0.0  0.0      0     0 ?        S    21:20   0:00 [kthreadd]
root           3  0.0  0.0      0     0 ?        I<   21:20   0:00 [rcu_gp]
root           4  0.0  0.0      0     0 ?        I<   21:20   0:00 [rcu_par_gp]
root           5  0.0  0.0      0     0 ?        I<   21:20   0:00 [slub_flushwq]
root           6  0.0  0.0      0     0 ?        I<   21:20   0:00 [netns]
root           8  0.0  0.0      0     0 ?        I<   21:20   0:00 [kworker/0:0H-events_highpri]
root          10  0.0  0.0      0     0 ?        I<   21:20   0:00 [mm_percpu_wq]
root          11  0.0  0.0      0     0 ?        S    21:20   0:00 [rcu_tasks_rude_]
```
Os campos exibidos com o `ps aux` (sem o hifen):
|Campo|Função|
|---|---|
|UID|Nome do usuário que é o dono do processo.|
|PID|Identificação do Processo.|
|%CPU|Percentual de consumo de CPU.|
|%MEM|Percentual de consumo de memória.|
|CMD|Comando executado.|

## O comando `top`
O comando `top` exibe os processos executados em tempo real. É diferente do comando `ps`, que mostra um instantâneo dos processos.

Alguns atalhos para modificar a exibição do comando `top`:
|Tecla|Função|
|---|---|
|`P`|Ordena os processos por consumo de CPU.|
|`M`|Ordena os processos por consumo de memória.|
|`N`|Ordena os processos por número de PID.|
|`T`|Ordena os processos pelo tempo de execução.|
|`L`|Permite filtrar os processos por uma string a ser informada (ex.: Apache).|
|`h`|Exibe a ajuda do comando.|
|`q`|Sai do comando.|

# Monitorando processos em tempo real
Instalando o Apache no Ubuntu:
```
 sudo apt install apache2
 ```

> Instalando o Apache em distribuições Red Hat:
> ```
> sudo yum install httpd
> ```

Confirmando se o Apache está funcionando (Ubuntu):
```
sudo service apache2 status
```

Comando no shell para disparar várias requisições e estressar o servidor Apache:
```
thiago@thiago-pc:~$ while :; do curl -l http://172.21.128.215; done
```
> O comando `while :; do curl -l http://172.21.128.215; done` na verdade é um loop infinito feito em uma linha. Cada ponto-e-vírgula representaria uma quebra de linha para manter conformidade com a sintaxe do shell.
> ```
> while :
>   do curl -l http://172.21.128.215
> done
> ```

Execute o comando `top` e depois pesquise por `apache` usando a tecla `L`. Depois pare as requisições feitas pelo loop infinito e compare o consumo de memória.

# Gerenciando processos com o Kill
O comando `kill` manda sinais para os processos. Informando o parâmetro `-l` vemos quais os tipos de sinais possíveis:
```bash
thiago@thiago-pc:/var/log$ kill -l
 1) SIGHUP       2) SIGINT       3) SIGQUIT      4) SIGILL       5) SIGTRAP
 6) SIGABRT      7) SIGBUS       8) SIGFPE       9) SIGKILL     10) SIGUSR1
11) SIGSEGV     12) SIGUSR2     13) SIGPIPE     14) SIGALRM     15) SIGTERM
16) SIGSTKFLT   17) SIGCHLD     18) SIGCONT     19) SIGSTOP     20) SIGTSTP
21) SIGTTIN     22) SIGTTOU     23) SIGURG      24) SIGXCPU     25) SIGXFSZ
26) SIGVTALRM   27) SIGPROF     28) SIGWINCH    29) SIGIO       30) SIGPWR
31) SIGSYS      34) SIGRTMIN    35) SIGRTMIN+1  36) SIGRTMIN+2  37) SIGRTMIN+3
38) SIGRTMIN+4  39) SIGRTMIN+5  40) SIGRTMIN+6  41) SIGRTMIN+7  42) SIGRTMIN+8
43) SIGRTMIN+9  44) SIGRTMIN+10 45) SIGRTMIN+11 46) SIGRTMIN+12 47) SIGRTMIN+13
48) SIGRTMIN+14 49) SIGRTMIN+15 50) SIGRTMAX-14 51) SIGRTMAX-13 52) SIGRTMAX-12
53) SIGRTMAX-11 54) SIGRTMAX-10 55) SIGRTMAX-9  56) SIGRTMAX-8  57) SIGRTMAX-7
58) SIGRTMAX-6  59) SIGRTMAX-5  60) SIGRTMAX-4  61) SIGRTMAX-3  62) SIGRTMAX-2
```
> Repare que o sinal SIGKILL (número 9) é o que força a interrupção dos processos.
> `O sinal SIGTERM (número 15) é o sinal default`. Ele termina o processo, mas antes realiza algumas tarefas prévias (importante para commitar gravações em bancos de dados, por exemplo).

Exemplo prático:
1. Rode o comando ping em loopback (`ping localhost`) num terminal à parte;
2. Em outro terminal, identifique o processo com `ps aux`;
3. Mande o sinal SIGKILL (9) para o comando de ping (`kill -9 <PID>`)
```bash
# Identificando o processo que executa o ping (no caso, o PID dele é 10854)
thiago@thiago-pc:/var/log$ ps aux | grep -i ping
thiago     10854  0.0  0.0   7716  1316 pts/1    S+   00:16   0:00 ping localhost
thiago     10870  0.0  0.1   6608  2396 pts/0    S+   00:24   0:00 grep --color=auto -i ping
# Identificando os campos usando ps aux | head
thiago@thiago-pc:/var/log$ ps aux | head
USER         PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND
root           1  0.0  0.6 102236 13232 ?        Ss   mai09   0:05 /sbin/init
...

# Matando o processo de ping.
thiago@thiago-pc:/var/log$ kill -9 10854

# Confirmando que o processo morreu.
thiago@thiago-pc:/var/log$ ps aux | grep -i ping
thiago     10874  0.0  0.1   6608  2412 pts/0    S+   00:24   0:00 grep --color=auto -i ping
# Note que a única linha do ping se refere ao comando `grep`, não ao ping realizado por outro terminal.
# Se você for ao terminal que executou o ping, ele vai mostrar a mensagem `Killed`.
thiago@thiago-pc:/var/log$
```

Outro exemplo, matando uma sessão SSH:
1. Rode o comando ping em loopback (`ping localhost`) num terminal à parte;
2. Em outro terminal, identifique o processo `ssh` com o PID mais alto (mais novo) usando `ps -ef`;
3. Mande um sinal SIGTERM (15) para o PPID que controla a sessão SSH (`kill -15 <PPID>`).
```bash
# Identificando o PID mais recente para sessão SSH.
# Repare que a sessão mais nova é representada pelo PPID 2646.
thiago@thiago-pc:/var/log$ ps -ef | grep -i ssh
root         718       1  0 mai09 ?        00:00:00 sshd: /usr/sbin/sshd -D [listener] 0 of 10-100 startups
root        1035     718  0 mai09 ?        00:00:00 sshd: thiago [priv]
thiago      1098    1035  0 mai09 ?        00:00:05 sshd: thiago@pts/0
root        2646     718  0 00:05 ?        00:00:00 sshd: thiago [priv]
thiago      2745    2646  0 00:05 ?        00:00:13 sshd: thiago@pts/1
thiago     10879    1099  0 00:29 pts/0    00:00:00 grep --color=auto -i ssh

# Termine com a sessão SSH enviando o sinal 15:
thiago@thiago-pc:/var/log$ sudo kill -15 2646
[sudo] password for thiago:

# Conferindo se o processo terminou.
thiago@thiago-pc:/var/log$ ps -ef | grep -i ssh
root         718       1  0 mai09 ?        00:00:00 sshd: /usr/sbin/sshd -D [listener] 0 of 10-100 startups
root        1035     718  0 mai09 ?        00:00:00 sshd: thiago [priv]
thiago      1098    1035  0 mai09 ?        00:00:05 sshd: thiago@pts/0
thiago     10886    1099  0 00:34 pts/0    00:00:00 grep --color=auto -i ssh

# Note que a saída do terminal afetado exibiu algumas mensagens que confirmaram
# o término suave da sessão:
# Connection to 172.21.128.215 closed by remote host.
# Connection to 172.21.128.215 closed.
thiago@thiago-pc:/var/log$
```
## O comando pstree
O comando `pstree` exibe os processos em formato de árvore:

```bash
thiago@thiago-pc:/var/log$ pstree
systemd─┬─ModemManager───2*[{ModemManager}]
        ├─apache2───2*[apache2───26*[{apache2}]]
        ├─cron
        ├─dbus-daemon
        ├─fwupd───4*[{fwupd}]
        ├─htcacheclean
        ├─irqbalance───{irqbalance}
        ├─login───bash
        ├─multipathd───6*[{multipathd}]
        ├─networkd-dispat
        ├─packagekitd───2*[{packagekitd}]
        ├─polkitd───2*[{polkitd}]
        ├─rsyslogd───3*[{rsyslogd}]
        ├─snapd───10*[{snapd}]
        ├─sshd─┬─sshd───sshd───bash───pstree
        │      └─sshd───sshd───bash
        ├─systemd───(sd-pam)
        ├─systemd-journal
        ├─systemd-logind
        ├─systemd-network
        ├─systemd-resolve
        ├─systemd-timesyn───{systemd-timesyn}
        ├─systemd-udevd
        ├─udisksd───4*[{udisksd}]
        ├─unattended-upgr───{unattended-upgr}
        └─upowerd───2*[{upowerd}]
```
> Perceba que o daemon `sshd` tem duas sessões SSH, e uma delas contém o comando `pstree`.

# Escalando privilégios com o Sudo
O arquivo `/etc/sudoers` lista os usuários que tem permissão de usar o comando `sudo`. Esse arquivo pode ser editado usando o comando `sudo visudo` (no exemplo, o editor usado é o nano).

Uma forma de verificar se o usuário atual é um sudoer é executando o comando `groups`:
```bash
thiago@thiago-pc:~$ groups
thiago adm cdrom sudo dip plugdev lxd
```
> Note que o usuário thiago participa dos grupos `thiago` (mesmo que o nome do usuário), `adm` e `sudo`.

O arquivo `/etc/groups` lista os grupos e para cada grupo os seus usuários são exibidos:

``` bash
thiago@thiago-pc:~$ cat /etc/group
root:x:0:
daemon:x:1:
bin:x:2:
sys:x:3:
adm:x:4:syslog,thiago
tty:x:5:
disk:x:6:
# ...
voice:x:22:
cdrom:x:24:thiago
floppy:x:25:
tape:x:26:
sudo:x:27:thiago
# ...
thiago:x:1000:
# ...
```

Filtrando com o grep:
```
thiago@thiago-pc:~$ cat /etc/group | grep thiago
adm:x:4:syslog,thiago
cdrom:x:24:thiago
sudo:x:27:thiago
dip:x:30:thiago
plugdev:x:46:thiago
lxd:x:110:thiago
thiago:x:1000:
```
> O comando usado para saber os grupos ao qual o usuário corrente pertence é `groups` no plural; o arquivo `/etc/group` está no singular.

O comando `su` serve para trocar de usuário no terminal:
```
thiago@thiago-pc:~$ sudo su
[sudo] password for thiago:
root@thiago-pc:/home/thiago#
```
```
thiago@thiago-pc:/var/log$ sudo su
[sudo] password for thiago:
root@thiago-pc:/var/log#
```
> Note que o cifrão foi substituído por tralha. A tralha indica que o usuário é privilegiado, enquanto o cifrão indica que o usuário não é privilegiado.

# Diferenças entre o root e os demais usuários
O comando `passwd` serve para mudar a senha do usuário atual:
```
thiago@thiago-pc:~$ passwd
Changing password for thiago.
Current password:
New password:
Retype new password:
passwd: password updated successfully
```

Definindo a senha do usuário root:
```
thiago@thiago-pc:~$ sudo su
root@thiago-pc:/home/thiago# passwd
New password:
Retype new password:
passwd: password updated successfully
root@thiago-pc:/home/thiago#
```
> Note o comando `sudo su` para forçar a mudança para o usuário root.

Modificando o usuário `thiago` a partir do usuário `root`:
```
root@thiago-pc:/home/thiago# passwd thiago
New password:
Retype new password:
passwd: password updated successfully
root@thiago-pc:/home/thiago#
```
> Note que, ao usarmos o usuário `root` para mudar a senha, não é necessário informar a senha antiga do usuário `thiago`.

O arquivo `/etc/passwd` contém os usuários criados no sistema:
```
thiago@thiago-pc:~$ cat /etc/passwd | grep thiago
thiago:x:1000:1000:Thiago:/home/thiago:/bin/bash
```
> Note que no fim de cada linha os dois últimos campos são o diretório home do usuário e o bash/terminal que ele usa. Note também que a senha não está presente no arquivo /etc/passwd.

O arquivo `/etc/shadow` contém o hash calculado a partir da senha. Quando o usuário informa a senha, o Linux gera o hash da senha e compara com o que está guardado no arquivo `/etc/shadow`:
```
thiago@thiago-pc:~$ sudo cat /etc/shadow | grep thiago
thiago:$y$j9T$vLBito6FAioyIWXF53UzP0$B4rTIqADEMAu8Mkbz.Jad2/VMsxjeLg1HdAhioyTj51:19487:0:99999:7:::
thiago@thiago-pc:~$ sudo cat /etc/shadow | grep root
root::19487:0:99999:7:::
```
> Note que o usuário root não tem senha (por isso está vazio entre os dois pontos depois do nome de usuário root).Os números que aparecem em seguida tem relação com tempo de expiração da senha.

# Usuários do sistema
Vamos comparar os usuários `thiago` e `www-data` (o dono do serviço do Apache):
```
thiago@thiago-pc:~$ grep www-data /etc/passwd
www-data:x:33:33:www-data:/var/www:/usr/sbin/nologin

thiago@thiago-pc:~$ grep thiago /etc/passwd
thiago:x:1000:1000:Thiago:/home/thiago:/bin/bash
thiago@thiago-pc:~$
```
> Note que o usuário `www-data` usa o shell `/usr/sbin/nologin`: ele não pode usar o shell. Os usuários comuns tem ID igual ou superior a 1000, enquanto os usuários de sistema tem ID inferior a mil.

Usuários sem bash associado não podem se autenticar:

```
thiago@thiago-pc:~$ su - www-data
Password:
su: Authentication failure
thiago@thiago-pc:~$ su www-data
Password:
su: Authentication failure
thiago@thiago-pc:~$
```
