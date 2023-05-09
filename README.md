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
