---
title: Instalar en producción LAMP de Ubuntu 16.04 con Apache, PHP 7, MySQL y SSL
layout: post
---

# Instalar en producción LAMP de Ubuntu 16.04 con Apache, PHP 7, MySQL y SSL

## Instalar MySQL
Ejecutar:

`apt-get -y install mysql-server mysql-client`

`mysql-server` y `mysql-client` son los llamados metapaquetes, los cuales siempre descargan desde Ubuntu la última versión de MySQL.

El siguiente paso es por razones de seguridad, y consiste en eliminar el usuario anónimo y la base de datos de prueba que vienen con la instalación, para ello debe ejecutarse:

 `mysql_secure_installation`

 A continuación se harán las siguientes preguntas, las respuestas se indican a continuación en negrita.

 Securing the MySQL server deployment.

 Enter password for user root: **Introducir la contraseña de MySQL**

VALIDATE PASSWORD PLUGIN can be used to test passwords
and improve security. It checks the strength of password
and allows the users to set only those passwords which are
secure enough. Would you like to setup VALIDATE PASSWORD plugin?

Press y|Y for Yes, any other key for No: **n**
Using existing password for root.

Change the password for root ? ((Press y|Y for Yes, any other key for No) : **Seleccionar y ó No según necesidades, para seguir este artículo no es necesario habilitar la validación de contraseña, con lo que se puede seleccionar n**

... skipping.
By default, a MySQL installation has an anonymous user,
allowing anyone to log into MySQL without having to have
a user account created for them. This is intended only for
testing, and to make the installation go a bit smoother.
You should remove them before moving into a production
environment.

Remove anonymous users? (Press y|Y for Yes, any other key for No) : **y**
Success

Normally, root should only be allowed to connect from
'localhost'. This ensures that someone cannot guess at
the root password from the network.

Disallow root login remotely? (Press y|Y for Yes, any other key for No) : **y**
Success

By default, MySQL comes with a database named 'test' that
anyone can access. This is also intended only for testing,
and should be removed before moving into a production
environment.

Remove test database and access to it? (Press y|Y for Yes, any other key for No) : **y**
- Dropping test database...
Success.

- Removing privileges on test database...
Success.

Reloading the privilege tables will ensure that all changes
made so far will take effect immediately.

Reload privilege tables now? (Press y|Y for Yes, any other key for No) : **y**
Success.

All done!

La configuración de MySQL ha sido securizada.


## Instalar Apache web server
Como Apache 2 esta disponible como paqueta de Ubuntu, podemos instalarlo con su comando correspondiente:

`apt-get -y install apache2`

Una vez terminado el proceso de instalación, pedimos en el navegador la IP del dominio donde lo hemos instalado, lo que nos debe mostrar la página predetermianda de Apache.

En Ubuntu, el directorio raíz predeterminado de Apache es /var/www/html, el archivo de configuración principal es /etc/apache2/apache2.conf, y la documentación del sistema de configuración está en /usr/share/doc/apache2/README.Debian.gz .


## Instalar PHP 7
Tanto PHP como el módulo de Apache PHP también estan disponibles como paquetes de Ubuntu, con lo que podemos ejecutarlos con su comando correspondiente:

`apt-get -y install php7.0 libapache2-mod-php7.0`

Tras terminar el proceso de instalación, es necesario reiniciar Apache con alguno de los siguientes comandos:

´systemctl restart apache2´

´service apache2 restart´

Si todo ha ido bien, Apache se reiniciara, lo que permitira hacer la comprobación de que PHP se ha instalado correctamente. Para ello debemos ir al directorio raíz predeterminado de Apache, que tal y como se indicaba en el punto anterior, por defecto es /var/www/html

En dicho directorio, crearemos un fichero llamado info y con la extensión .php, dentro del cual escribiremos:

		<?php 
			phpinfo(); 
		?>

después, debemos cambiar el propietario del archivo recien creado llamado info.php al usuario y grupo de www-data con el siguiente comando:

´chown www-data:www-data /var/www/html/info.php´

A continuación, pediremos al navegador la URL correspondiente, añadiendole al final /info.php, lo que nos tiene que devolver una página con información sobre PHP.

Si nos fijamos en la información que ofrece, podemos ver como en la parte de módulos habilitados MySQL no aparece, el motivo es porque no esta instalado ningun paquete de MySQL, y para ello debemos ejecutar el comando:

´apt-cache search php7.0´

después, seleccionamos los que hagan falta y los instalamos, estos son algunos de ejemplo:

´apt-get -y install php7.0-mysql php7.0-curl php7.0-gd php7.0-intl php-pear php-imagick php7.0-imap php7.0-mcrypt php-memcache  php7.0-pspell php7.0-recode php7.0-sqlite3 php7.0-tidy php7.0-xmlrpc php7.0-xsl php7.0-mbstring php-gettext´

despues de la instalación, es necesario reiniciar Apache con alguno de los siguientes comandos:

´systemctl restart apache2´

´service apache2 restart´

Si ahora volvemos a comprobar la parte de módulos habilitados, veremos como aparece MySQL


## Instalar Opcache + APCu PHP cache para acelerar PHP
PHP 7 se envía con un identificador de código de operación incorporado para el almacenamiento en caché y la optimización del código intermedio de PHP, tiene el nombre 'opcache' y está disponible en el paquete php7.0-opcache. Se recomienda tener un Opcache instalado para acelerar la página PHP.

Además de opcache, instalar APCu, que es un contenedor de compatibilidad para opcache para proporcionar las funciones de la memoria caché de APC, un sistema de caché de uso frecuente en las versiones de PHP 5.x y que muchos sistemas CMS todavía usan.

Opcache y APCu se instalan de la siguiente manera:

´apt-get -y install php7.0-opcache php-apcu´

Si se indicase que ya esta instalado no pasaría nada, ahora es necesario reiniciar Apache con alguno de los siguientes comandos:

´systemctl restart apache2´

´service apache2 restart´


## Habilitar SSL en Apache
Para conseguir un certificado SSL, es recomendable usar Certbot, ya que en los sistemas Ubuntu el equipo de Certbot mantiene un (PPA)[https://help.ubuntu.com/community/PPA], y una vez que lo agregue a su lista de repositorios todo lo que tendrá que hacer es obtener los siguientes paquetes (poner sudo delante de todos los comandos si son necesarios permisos de administrador):

apt-get update
apt-get install software-properties-common
add-apt-repository ppa:certbot/certbot
apt-get update
apt-get install python-certbot-apache

Certbot tiene un plugin Apache bastante sólido en beta, que es compatible con muchas plataformas y automatiza la instalación de certificados.

certbot --apache

Al ejecutar dicho comando, se obtendrá un certificado y dejara que Certbot edite su configuración de Apache automáticamente para servirlo. Para unos cambios manuales a la configuración de Apache, usar el subcomando certonly:

certbot --apache certonly

(https://certbot.eff.org/docs/)[Documentación] para más información sobre cómo usar Certbot.

Los paquetes de Certbot vienen con una tarea cron, la cual renovará los certificados automáticamente antes de que caduquen, y como los certificados de Let's Encrypt duran 90 días, es recomendable aprovechar esta funcionalidad, para ello debe ejecutarse este comando:

certbot renew --dry-run

Para una información más detallada y opciones sobre la renovación consultar la (https://certbot.eff.org/docs/using.html#renewal)[documentación]

Por último, para desabilitar el site de ejemplo que aparece durante la instalación, debe ejecutarse:

a2dissite default-ssl


## Instalar phpMyAdmin
phpMyAdmin es un gestor de bases de datos MySQL basado en interfaz web, y que se recomienda para gestionar dichas bases de datos.

Para ello se debe ejecutar:

´apt-get -y install phpmyadmin´

Durante el proceso de instalación, el instalador hara una serie de preguntas, hay que tener presente que para seleccionar un elemento del menú de la primera pregunta, se debe seleccionar usando el tabulador o las flechas de dirección y después presionar la barra del espacio, esta acción mostrara un asterisco entre los corchetes del elemento, indicando que ha sido seleccionado.

Web server to configure automatically: <-- seleccionar: apache2
Configure database for phpmyadmin with dbconfig-common? <-- Yes
MySQL application password for phpmyadmin: <-- si se desea, presionar la tecla enter para que apt cree una contraseña añeatoria.

Tras finalizar el proceso, pedir al navegador la URL del dominio seguido de /phpmyadmin, el navegador debe mostrar el formulario de login.

Fuentes
https://www.howtoforge.com/tutorial/install-apache-with-php-and-mysql-on-ubuntu-16-04-lamp/
https://certbot.eff.org/lets-encrypt/ubuntuxenial-apache
http://debiantotal.blogspot.com.es/2007/04/manual-bsico-de-vim-editor-texto-en.html
https://www.ssllabs.com/ssltest/analyze.html?d=criteriosroma4.es
https://www.ssllabs.com/ssltest/analyze.html
