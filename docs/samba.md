# Samba 4  

Material utilizado para instalar e administrar o samba 4 como controlador de domínio

Neste tutorial foram usados os seguintes softwares:

* Debian 10 64 bits
* Samba 4.9.5 - Instalado via repositório oficial do debian





### Samba 4 como controlador de domínio adicional

Altere o hostname do servidor

	#  hostnamectl set-hostname ad2			

Adicione no `/etc/hosts`, uma entrada com o nome e IP da máquina que será adicionada como membro e do AD principal

	 10.0.99.10		ad2.domain.lan		ad2			
	 10.0.99.11		ad1.domain.lan		ad1			

Adicione o IP do ad principal ao arquivo `/etc/resolv.conf`

	search domain.lan
	nameserver 10.0.99.11

Instale o pacote ntp
```
# apt install ntp

```
Comente as entradas que contém os sevidores de nome e adicione as seguintes:

	pool pool.ntp.br iburst

Reinicie o serviço e adicione sua inicialização junto com o SO

	# systemctl restart ntp
	# systemctl enable ntp

Instale os seguintes pacotes

```
# apt install samba krb5-user krb5-config winbind libpam-winbind libnss-winbind
```
Durante a instalação, será pedido o nome krb5, que será o nome do seu domínio `domain.lan`

Após a instalação, faça o teste para ver se o servidor está conseguindo tickets do servidor principal

```
# kinit administrator@domain.lan
```

Verifique se o servidor conseguiu o ticket

```
# klist
```

Se o servidor conseguir o ticket, pare os serviços associados ao samba

```
# systemctl stop samba-ad-dc smbd nmbd winbind
```

Renomeie o arquivo original do samba

```
# mv /etc/samba/smb.conf /etc/samba/smb.original
```

Adicione o servidor como DC secundário

```
# samba-tool domain join domain.lan DC -U"administrator" --dns-backend=SAMBA_INTERNAL
```
O dns interno do samba é bem limitado e não é recursivo, para fazer com que os hosts, que usam o samba para acessar à internet ou outros nomes, que o samba não conhece, é necessário adicionar a seguinte linha ao arquivo `/etc/samba/smb.conf`
```
# dns forwarder = 8.8.8.8
```

Restarte o serviço do samba
```
# systemctl restart samba-ad-dc
```

Use o seguinte comando para verificar a replicação os dados
```
# samba-tool drs showrepl
```

Copie o arquivo de configuração do kerberos criado pelo samba para o diretório `/etc`
```
# mv /etc/krb5.conf /etc/krb5.original
# ln -s /var/lib/samba/private/krb5.conf /etc/
```

Valide as entradas dns do samba
```
# host domain.lan
# host -t SRV _kerberos._udp.domain.lan
# host -t SRV _ldap._tcp.domain.lan
```
O comando anterior deverá ter como saída algo parecido com isso

```
root@ad2# host domain.lan 
root@ad2# domain.lan has address 10.0.99.10
root@ad2# domain.lan has address 10.0.99.11
root@ad2# host -t SRV _ldap._tcp.domain.lan
root@ad2# _ldap._tcp.domain.lan has SRV record 0 100 389 ad1.domain.lan
root@ad2# _ldap._tcp.domain.lan has SRV record 0 100 389 ad2.domain.lan
root@ad2# host -t SRV _kerberos._udp.domain.lan
root@ad2# _kerberos._udp.domain.lan has SRV record 0 100 88 ad1.domain.lan
root@ad2# _kerberos._udp.domain.lan has SRV record 0 100 88 ad2.domain.lan
```
Desabilite os serviços que não serão necessários e habilite o samba para iniciar junto com o SO
```
# systemctl disable smbd nmbd winbind
# systemctl enable samba-ad-dc
```
