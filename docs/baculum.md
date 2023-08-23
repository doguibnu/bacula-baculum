## Instalar Baculum 11.06

Fazer update e upgrade do sistema com o comando:

```
yum update -y && yum upgrade -y 
```

Instalar os pacotes necessários para compilar e instalar o Baculum:

```
yum install -y nano wget tar httpd php php-common php-pdo php-pgsql php-bcmath php-json php-xml php-ldap
```

Criar um grupo bacula:

```
groupadd bacula
```

Fazer um diretório temporário para baixar o Baculum com o comando:

```
mkdir -p /var/www/html/baculumtemp
```

Mudar para o diretório criado com o comando:

```
cd /var/www/html/baculumtemp
```

Baixar o baculum 11.06 do site com o comando:

```
wget --no-check-certificate https://sourceforge.net/projects/bacula/files/bacula/11.0.6/bacula-gui-11.0.6.tar.gz
```

Descompactar o arquivo baixado com o comando:

```
tar -xvf bacula-gui-11.0.6.tar.gz
```

Criar o diretório /srv/[www.htdocs/baculum](http://www.htdocs/baculum) com o comando:

```
mkdir -p /srv/www/htdocs/baculum
```

Mudar para o diretório do arquivo descompactado:

```
cd /var/www/html/baculumtemp/bacula-gui-11.0.6 
```

**Preparar o DESTINO temporário e construção de arquivos. Utilizar o comando:
make build DESTDIR=/tmp/baculum-files WWWDIR=/srv/www/htdocs/baculum**

Seguindo o exemplo acima, faça o comando:



Sendo **DESTDIR=`/var/www/html/baculumtemp/bacula-gui-11.0.6/baculum`** e **WWWDIR=`/srv/www/htdocs/baculum`**

Então:

```
make build DESTDIR=/var/www/html/baculumtemp/bacula-gui-11.0.6/baculum WWWDIR=/srv/www/htdocs/baculum 
```

Dentro do diretório **/var/www/html/baculumtemp/bacula-gui-11.0.6/baculum** listando seu conteúdo, irá perceber que terá um novo subdiretório: **srv**, então: Copiar os arquivos do tipo web para o diretório web do sistema:

```
cp -R srv/www/htdocs/baculum/ /srv/www/htdocs/
```

Copiar os arquivos de configuração web do Apache para /etc/httpd/conf.d/:

```
cp /var/www/html/baculumtemp/bacula-gui-11.0.6/baculum/etc/httpd/conf.d/baculum-*conf /etc/httpd/conf.d/
```

Copiar os arquivos básicos de autenticação:

API:

```
cp /var/www/html/baculumtemp/bacula-gui-11.0.6/baculum/etc/baculum/Config-api-apache/baculum.users /srv/www/htdocs/baculum/protected/API/Config
```

Web:

```
cp /var/www/html/baculumtemp/bacula-gui-11.0.6/baculum/etc/baculum/Config-web-apache/baculum.users /srv/www/htdocs/baculum/protected/Web/Config
```

Copiar os arquivos de localização (API): English, Pl e PT-BR:

```
cp --remove-destination /var/www/html/baculumtemp/bacula-gui-11.0.6/baculum/usr/share/locale/en/LC_MESSAGES/baculum-api.mo /srv/www/htdocs/baculum/protected/API/Lang/en/messages.mo
cp --remove-destination /var/www/html/baculumtemp/bacula-gui-11.0.6/baculum/usr/share/locale/pl/LC_MESSAGES/baculum-api.mo /srv/www/htdocs/baculum/protected/API/Lang/pl/messages.mo
cp --remove-destination /var/www/html/baculumtemp/bacula-gui-11.0.6/baculum/usr/share/locale/pt/LC_MESSAGES/baculum-api.mo /srv/www/htdocs/baculum/protected/API/Lang/pt/messages.mo
```

Copiar os arquivos de localização (WEB): English, Pl, PT-BR e JA:

```
cp --remove-destination /var/www/html/baculumtemp/bacula-gui-11.0.6/baculum/usr/share/locale/en/LC_MESSAGES/baculum-web.mo /srv/www/htdocs/baculum/protected/Web/Lang/en/messages.mo
cp --remove-destination /var/www/html/baculumtemp/bacula-gui-11.0.6/baculum/usr/share/locale/pl/LC_MESSAGES/baculum-web.mo /srv/www/htdocs/baculum/protected/Web/Lang/pl/messages.mo
cp --remove-destination /var/www/html/baculumtemp/bacula-gui-11.0.6/baculum/usr/share/locale/pt/LC_MESSAGES/baculum-web.mo /srv/www/htdocs/baculum/protected/Web/Lang/pt/messages.mo
cp --remove-destination /var/www/html/baculumtemp/bacula-gui-11.0.6/baculum/usr/share/locale/ja/LC_MESSAGES/baculum-web.mo /srv/www/htdocs/baculum/protected/Web/Lang/ja/messages.mo
```

Setar o proprietário e grupo dos arquivos copiados:

```
chown -R apache:apache /srv/www/htdocs/baculum/
```

Mudar para o diretório: /var/www/html/baculumtemp/bacula-gui-11.0.6/baculum/etc com o comando:

```
cd /var/www/html/baculumtemp/bacula-gui-11.0.6/baculum/etc
```

Copiar Recursivamente o subdiretório baculum para **/etc**:

```
cp -r baculum /etc/
```

Mudar a permissão do grupo recursivamente para leitura, escrita e execução:

```
chmod -R g+rwx /etc/baculum/
```

Mudar o proprietário do subdiretório /etc/baculum/:

```
chown -R apache:bacula /etc/baculum/
```

No Terminal mudar as Políticas de segurança para o sudo não requisitar senha dos seguintes caminhos:

```
echo "Defaults:wwwrun "'!'"requiretty
wwwrun ALL=NOPASSWD:  /usr/sbin/bconsole
wwwrun ALL=NOPASSWD:  /usr/sbin/bdirjson
wwwrun ALL=NOPASSWD:  /usr/sbin/bsdjson
wwwrun ALL=NOPASSWD:  /usr/sbin/bfdjson
wwwrun ALL=NOPASSWD:  /usr/sbin/bbconsjson
wwwrun ALL=(root) NOPASSWD: /usr/bin/systemctl start bacula-dir
wwwrun ALL=(root) NOPASSWD: /usr/bin/systemctl stop bacula-dir
wwwrun ALL=(root) NOPASSWD: /usr/bin/systemctl restart bacula-dir
wwwrun ALL=(root) NOPASSWD: /usr/bin/systemctl start bacula-sd
wwwrun ALL=(root) NOPASSWD: /usr/bin/systemctl stop bacula-sd
wwwrun ALL=(root) NOPASSWD: /usr/bin/systemctl restart bacula-sd
wwwrun ALL=(root) NOPASSWD: /usr/bin/systemctl start bacula-fd
wwwrun ALL=(root) NOPASSWD: /usr/bin/systemctl stop bacula-fd
wwwrun ALL=(root) NOPASSWD: /usr/bin/systemctl restart bacula-fd
" > /etc/sudoers.d/baculum
```

Fazer o bacula pertencer ao grupo apache:

```
usermod -aG bacula apache
```

Mudar o dono e o Grupo recursivamente dos diretórios:

```
chown -R apache:bacula /opt/bacula/working /opt/bacula/etc
```

Prover permissão de leitura, escrita e execução para o grupo no diretório (rwx):

```
chmod -R g+rwx /opt/bacula/working /opt/bacula/etc
```

Criar um diretório em **/var/log/apache2/apierror** e **/var/log/apache2/apierrorlog:**

```
mkdir -p /var/log/apache2/apierror
mkdir -p /var/log/apache2/apierrorlog
```

Editar o arquivo **/etc/httpd/conf.d/baculum-api.conf** e alterar as linhas:

```
nano /etc/httpd/conf.d/baculum-api.conf
CustomLog /var/log/apache2/apierror/baculum-api-access.log combined
ErrorLog /var/log/apache2/apierrorlog/baculum-api-error.log
```

Salvar o arquivo

Criar um diretório em **/var/log/apache2/weberror** e **/var/log/apache2/weberrorlog:**

```
mkdir -p /var/log/apache2/weberror
mkdir -p /var/log/apache2/weberrorlog
```

Editar o arquivo **/etc/httpd/conf.d/baculum-web.conf** e alterar as linhas:

```
nano /etc/httpd/conf.d/baculum-web.conf
CustomLog /var/log/apache2/weberror/baculum-web-access.log combined
ErrorLog /var/log/apache2/weberrorlog/baculum-web-error.log
```

Salvar o arquivo

Abrir portas **9095** e **9096** no firewall e recarregar firewall:

```
firewall-cmd --permanent --zone=public –add-port=9095-9096/tcp
firewall-cmd --reload
```

Desabilitar o SELinux com o comando:

```
nano /etc/sysconfig/selinux
Setar SELINUX=enforcing para SELINUX=disbaled
```

Salvar o arquivo

Reiniciar o sistema:

```
reboot
```

**Configuração da API do Baculum**

```
http://IP-server:9096
user: admin
senha: admin
```

**Configuração Baculum web**

```
http://IP-server:9095
user: admin
senha: admin
```

Fontes:

Instalação [Bacula](https://www.bacula.lat/community/comandos-de-compilacao/)

[Wanderlei Huttel](https://github.com/wanderleihuttel/bacula-utils/tree/master/tutorial)

Instalação Baculum:

[Instalação Manual do Baculum](https://baculum.app/doc/brief/installation.html#manual-installation)

[Site Bacula.lat](https://www.bacula.lat/community/baculum/)
