# Instalação do agente zabbix em servidores Linux Debian

###### Instale o repositório


```
# url='https://repo.zabbix.com/zabbix/4.5/debian/pool/main/z/zabbix-release/zabbix-release_4.5-1%2Bbuster_all.deb'
# wget $url
# dpkg -i zabbix-release_4.5-1+buster_all.deb
# apt update -y

```

###### Instale o zabbix-agent


```
# apt install zabbix-agent zabbix-get -y

```

##### Configure o agente


```
# sed -i 's/Hostname=Zabbix server/Hostname='$(hostname)'/g' zabbix_agentd.conf

# sed -i 's/Server=127.0.0.1/Server=zabbix.infra.ifpe.edu.br/g' zabbix_agentd.conf 

# sed -i 's/ServerActive=127.0.0.1/ServerActive=zabbix.infra.ifpe.edu.br/g' zabbix_agentd.conf 

# sed -i 's/# LogRemoteCommands=0/LogRemoteCommands=1/g' zabbix_agentd.conf 

# sed -i 's/# EnableRemoteCommands=0/EnableRemoteCommands=1/g' zabbix_agentd.conf 

# sed -i 's/# StartAgents=3/StartAgents=3/g' zabbix_agentd.conf

```

###### Habilite o agente para iniciar no boot do sistema e dê o start no agente


```
# systemctl enable zabbix-agent
# systemctl start zabbix-agent

```

