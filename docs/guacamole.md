
# Instale o tomcat 9

```
# apt install tomcat9 tomcat9-admin tomcat9-common tomcat9-user

```
## Instalação do Guacamole Server



*  Instale as dependências

```
# apt install build-essential libcairo2-dev libjpeg62-turbo-dev libtool-bin libossp-uuid-dev libavcodec-dev libavutil-dev libswscale-dev freerdp2-dev libpango1.0-dev libssh2-1-dev libtelnet-dev libvncserver-dev libwebsockets-dev libpulse-dev libssl-dev    libvorbis-dev libwebp-dev

```


* Faça download do pacote

```
# url='http://apache.org/dyn/closer.cgi?action=download&filename=guacamole/1.2.0/source/guacamole-server-1.2.0.tar.gz'
# wget $url -O guacamole-server.tar.gz

```
* Descompacte o pacote

```
# tar -xvf guacamole-server.tar.gz

```

* Entre no diretório do guacamole e faça a instalação

```
# cd guacamole-server-1.2.0
# ./configure --with-init-dir=/etc/init.d
# make ; make install
# ldconfig
```
* Ative a inicialização no boot e start o serviço

```
# systemctl enable guacd
# systemctl start guacd

```
## Configuração do Guacamole Client


 * Faça download do client

```

# site='http://apache.org/dyn/closer.cgi?action=download&filename=guacamole/1.2.0/binary/guacamole-1.2.0.war'
# wget $site -O guacamole.war
# mkdir /etc/guacamole
# cp guacamole.war /etc/guacamole/guacamole.war
# ln -s /etc/guacamole/guacamole.war /var/lib/tomcat9/webapps/
# mkdir /etc/guacamole/{extensions,lib}
# echo "GUACAMOLE_HOME=/etc/guacamole" | tee -a /etc/default/tomcat9

```

# Instale o banco de dados

```
# apt install mariadb-server mariadb-client

```

* Crie o banco de dados e o usuário para acessar o banco

```
# mysql -p
# CREATE DATABASE guacamole_db;
# CREATE USER 'guacamole_user'@'localhost' IDENTIFIED BY 'passw0rd';
# GRANT SELECT,INSERT,UPDATE,DELETE ON guacamole_db.* TO 'guacamole_user'@'localhost';
# FLUSH PRIVILEGES;
# quit;

```

* Faça o download do jdbc-extension

```
# wget http://apache.mirror.digionline.de/guacamole/1.2.0/binary/guacamole-auth-jdbc-1.2.0.tar.gz
# tar vfx guacamole-auth-jdbc-1.2.0.tar.gz

```

* Importe a base de dados

```
#  cat guacamole-auth-jdbc-1.2.0/mysql/schema/*.sql | mysql -u root -p guacamole_db

```

* Instale a extensão

```
# cp guacamole-auth-jdbc-1.2.0/mysql/guacamole-auth-jdbc-mysql-1.2.0.jar /etc/guacamole/extensions/

```

* Instale o driver JDBC

```
# wget https://dev.mysql.com/get/Downloads/Connector-J/mysql-connector-java-8.0.13.tar.gz

# tar xvzf mysql-connector-java-8.0.13.tar.gz

# cp mysql-connector-java-8.0.13/mysql-connector-java-8.0.13.jar /etc/guacamole/lib/

```

* Configure o timezone do banco de dados

```
# mysql_tzinfo_to_sql /usr/share/zoneinfo | mysql -u root -p mysql

```
Edite o arquivo `/etc/mysql/mariadb.conf.d/50-server.cnf` e adicione o seguinte conteúdo

```
# default_time_zone='America/Recife'

```

* Reinicie o banco de dados

```
# systemctl restart mariadb.service

```

# Configure o Guacamole


Edite o arquivo `/etc/guacamole/guacamole.properties` e adicione o seguinte conteúdo

```
# Hostname and Guacamole server port
 guacd-hostname: localhost
 guacd-port: 4822

# MySQL properties
mysql-hostname: localhost
mysql-port: 3306
mysql-database: guacamole_db
mysql-username: guacamole_user
mysql-password: passw0rd

```

Reinicie os serviços

```
# systemctl restart tomcat9
# systemctl restart guacd

```

Acesse o seguinte endereço `http://IP_SERVER:8080/guacamole`

O usuário e senha são `guacadmin` e `guacadmin`

# Configure a autenticação LDAP

Baixe a extensão

```
# wget https://downloads.apache.org/guacamole/1.2.0/binary/guacamole-auth-ldap-1.2.0.tar.gz
# tar xvf guacamole-auth-ldap-1.2.0.tar.gz
# cp guacamole-auth-ldap-1.2.0/guacamole-auth-ldap-1.2.0.jar /etc/guacamole/extensions/

```

Adicione o seguinte conteúdo ao arquivo `/etc/guacamole/guacamole.properties`

```
### LDAP ###


ldap-hostname:ldap.domain.lan # IP ou nome do servidor ldap
ldap-port:389
ldap-encryption-method: none
ldap-search-bind-dn:CN=leitor-guacamole,OU=SERVICES,DC=domain,DC=lan  # Usuário com permissão de leitura na base ldap
ldap-search-bind-password:XXXsenhaXXX
ldap-user-base-dn:DC=domain,DC=lan        # Onde buscar os usuários
ldap-group-base-dn:OU=GROUPS,DC=domain,DC=lan   # Onde o guacamole irá buscar os grupos
ldap-group-name-attribute:cn
ldap-username-attribute: sAMAccountName
ldap-follow-referrals: false
ldap-operation-timeout: 30
ldap-user-search-filter: (&(objectClass=user)(memberOf=CN=gg_guacamole_rdp,OU=GROUPS,DC=domain,DC=lan))  # Filtro para buscar apenas usuários que pertençam ao grupo gg_guacamole_rdp

```

# Prepare a base Ldap

Crie os usuários leitor-guacamole e o grupo gg_guacamole_rdp

Depois de criados os usuários, adicione um usuário do domínio ao grupo gg_guacamole_rdp e faça login no sistema com o usuário padrão `guacadmin`

# Configure os usuários no Guacamole

* Clique no canto superior direito, no nome guacadmin e depois em settings, clique agora em users e adicione o usuário que foi adicionado ao grupo gg_guacamole_rdp, não é necessário colocar a senha. 
* Marque todos os checkboxes da guia permissions. 
* Faça logoff e, depois, login com o usuário recém criado.
* Vá na guia users e verá que todos os usuários do Ldap que, se adequam aos atributos configurados no filtro, estaram listados.
 

# Configure o proxy reverso

```

server {
   listen 80;
   server_name remoto.domain.lan;

   access_log /var/log/nginx/remoto.domain.lan-access-80.log;
   error_log /var/log/nginx/remoto.domain.lan-error.log;

   include /etc/nginx/proxy_params;

   location / {
      return 301 https://$host$request_uri;
   }
}

server {
   listen 443 ssl;
   server_name remoto.domain.lan;

   access_log /var/log/nginx/remoto.domain.lan-access-443.log;
   error_log /var/log/nginx/remoto.domain.lan-error.log;

  include              /etc/nginx/ssl/domain_ssl_params;
  include              /etc/nginx/ssl/domain_ssl_crt;

   include /etc/nginx/proxy_params;

   location / {
    proxy_pass http://IP_SERVER:8080/guacamole/;
    proxy_buffering off;
    proxy_http_version 1.2;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    proxy_set_header Upgrade $http_upgrade;
    proxy_set_header Connection $http_connection;
    access_log off;
   }
}


```

