# Dicas Linux 

Documentação com dicas sobre o Linux

### Aumentar o swap no Linux


No exemplo a seguir, será criado um arquico com 1 GB de tamanho que será usado como swap.


```
 # dd if=/dev/zero of=/root/novo_swap bs=1M count=1024

```
Vamos definir permissões de acesso para somente o root.

```
# chmod 600 /root/novo_swap

```
Vamos formatar o arquivo com o tipo swap.

```
# mkswap /root/novo_swap

```

Use o seguinte comando para indicar o arquivo como incremento ao swap.

```
# swapon /root/novo_swap

```

Faça com que o novo arquivo seja montado durante o boot

```
# echo "/root/novo_swap none swap sw 0 0" >> /etc/fstab

```

Digite o seguinte comando para montar as partições presentes no fstab.

```
# mount -a

```

### Problemas para atualizar repositório php sury

Apague o arquivo contendo o repositório em '/etc/apt/sources.list.d/'

Depois, digite os seguintes comandos para criar o novo repositório

```
wget https://packages.sury.org/php/apt.gpg -O /etc/apt/trusted.gpg.d/php-sury.gpg
echo "deb https://packages.sury.org/php/ $(lsb_release -sc) main" > /etc/apt/sources.list.d/php-sury.list

```



