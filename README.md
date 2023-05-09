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
