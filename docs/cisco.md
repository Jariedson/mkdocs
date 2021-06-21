### Configurar NTP

Para configurar o ntp como um nome é preciso antes configurar o dns do switch



```
Swt# configure terminal
Swt(config)# ip name-server 8.8.8.8
Swt(config)# end
Swt# copy running-config startup-config
```

Depois de configurar o dns do switch é possível configurar o ntp por nome


```
Swt#  configure terminal
Swt(config)#  ntp server pool.ntp.br
Swt(config)#  clock timezone BR -3 0
Swt(config)# exit
Swt# show ntp associations
Swt#  show clock
Swt# copy running-config startup-config

```


### Configurar link-aggregation

Antes de iniciar a configuração, retorne as interfaces usadas para o estado inicial da seguinte forma:


```
Swt(config)# default interface gigabitEthernet 0/21
Swt(config-if)# default interface gigabitEthernet 0/22

```

Depois, inicie a configuração do link aggregation

```
Swt(config)# interface gigabitethernet0/21
Swt(config-if)# channel-group 1 mode passive
Swt(config-if)# channel-protocol lacp
Swt(config-if)# exit
Swt(config)# interface gigabitethernet0/22
Swt(config-if)# channel-group 1 mode passive
Swt(config-if)# channel-protocol lacp
Swt(config-if)# exit

```

Agora, configure a interface em modo trunk e informe as vlans que pasarão por ela

```
Swt# conf t
Swt(config)# interface Port-channel 1
Swt(config)# switchport mode trunk
Swt(config-if)# switchport trunk native vlan 1
Swt(config-if)# switchport trunk allowed vlan add 1,100,200
Swt(config-if)# exit
Swt(config)# do wr

```

### Configurar SNMP


No switch, crie a comunidade com permissão de leitura, defina o e-mail de contato e a localização do equipamento


```

Swt# conf t
Swt(config)# snmp-server community it-manager ro
Swt(config)# snmp-server contact Admin-Network <admin-network@domain.lan>
Swt(config)# snmp-server location it room
Swt(config)# exit
Swt# copy running-config startup-config

```

