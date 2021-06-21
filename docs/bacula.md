# Instalação do bacula através de script de instalação

Use o seguinte comando para fazer download do script

```
wget -c https://raw.githubusercontent.com/wanderleihuttel/bacula-utils/master/conf/scripts/_bacula_community_package.sh -O /usr/local/bin/_bacula_community_install.sh

```

Dê permissão e execute o script

```
chmod a+x /usr/local/bin/_bacula_community_install.sh
/usr/local/bin/_bacula_community_install.sh

```

Nas próximas telas, informe o seu ID (5f98339530de8), que é fornecido no e-mail de cadastro no bacula community.

```
--------------------------------------------------
# Informe sua chave do Bacula 
# Esta chave é obtida com o registro em Bacula.org
# http://blog.bacula.org/download-community-binaries
Please, fill with your Bacula Key:

```

Depois, informe a versão a ser instalada.

```
# Este script só funcionará com as versões mais recentes do Debian e do CentOS
--------------------------------------------------
Inform the Bacula version
  - 9.0.0
  - 9.0.4
  - 9.0.5
  - 9.0.6
  - 9.0.7
  - 9.0.8
  - 9.2.0
  - 9.2.1
  - 9.2.2
Choose your Bacula Version:

```

Informe agora o banco que será usado na instalação.

```
What do you want to do?
  1) Install Bacula with PostgreSQL
  2) Install Bacula with MySQL
  3) Exit
Select an option [1-3]:

```

Aguarde a conclusão do script e depois já será possível iniciar a configuração do servidor.


# Configuração do Director
