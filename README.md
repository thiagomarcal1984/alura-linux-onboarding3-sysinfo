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
