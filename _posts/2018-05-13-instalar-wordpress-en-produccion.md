---
title: Instalar WordPress en producción
layout: post
---

# Instalar WordPress en producción

+ en el directorio /opt tenemos que crear un directorio llamado wordpress, para ello debemos llegar a él moviéndonos a traves de los directorios usando la línea de comandos del PuTTY. Si se ha iniciado sesión como root, el terminal nos colocara en el directorio /root, con lo que tendremos que subir un nivel en la estructura de directorios para colocarnos en la raiz.

+ una vez en la raiz ( / ), listamos los directorios y veremos como ahora si aparece el directorio opt

+ nos cambiamos al directorio opt usando cd opt

+ una vez dentro, creamos el directorio wordpress usando mkdir wordpress, podemos verificar que se ha creado actualizando la ventana de WinSCP, o listando los directorios y ficheros con la terminal usando ls

+ el siguiente paso es instalar Git, para ello usamos el comando apt-get install git
()[https://git-scm.com/book/es/v2/Inicio---Sobre-el-Control-de-Versiones-Instalaci%C3%B3n-de-Git], desde cualquier directorio, ya que la instalación sera a nivel global

+ despues tenemos que movernos al directorio de wordpress antes creado usando cd wordpress, con lo que al hacer pwd, nos mostrara /opt/wordpress

+ después clonamos el repo de wordpress, usando git clone ruta-del-repositorio[espacio]. , para este caso específico sería desde Bitbucket, quedando el comando asi

git clone https://srmarin@bitbucket.org/omikronicysem/wordpress.git [espacio] .

+ tras el proceso, podemos listar los archivos y directorios de wordpress usando ls

+ el siguiente paso sera crear con phpmyadmin una base de datos vacia para wordpress, un usuario, y asociarlo a dicha base de datos con todos los privilegios

+ después, se debe hacer que el DocumentRoot del host virtual de Apache apunte directamente a la carpeta donde hemos instalado los ficheros de WordPress, en este caso a /opt/wordpress, para ello nos dirigimos a /etc/apache2/sites-available, y editamos el fichero 000-default-le-ssl.conf

+ en dicho fichero, cambiamos el valor de DocumentRoot por /opt/wordpress, guardamos y reiniciamos Apache con service apache2 restart

+ tras el reinicio, para comprobar que todo ha ido bien, debemos pedir en el navegador el domino correspondiente y, en lugar de devolvernos el instalador de WordPress, nos debe devolver un error 403 (Forbidden)

+ dicho error ocurre porque Apache se ejecuta con el usuario www-data, y la carpeta donde se han instalado los ficheros de WordPress se creo con el usuario root, con lo que Apache no tiene permisos por defecto para procesar los ficheros de dicho directorio

+ para cambiar el propietario del directorio, se debe ejecutar el comando chown www-data:www-data -R /opt/wordpress

+ tras el cambio de propietario, como Apache tiene un mecanismo de seguridad que por defecto no permite acceder a contenido fuera de la estructura de carpetas original de Apache (recordemos que el directorio que guardan los ficheros de WordPress lo hemos creado nosotros), hay que añadir la directiva Directory apuntando al directorio correpondiente, en nuestro caso /opt/wordpress.

+ para ello debemos editar de nuevo el fichero 000-default-le-ssl.conf y despues de las instrucciones ya declaradas añadir:

	<Directory /opt/wordpress>
		Options Indexes FollowSymLinks 	--> opciones comunes de configuración
		AllowOverride All 							--> permite el uso de ficheros .htaccess en carpetas
		Require all granted 						--> permite el acceso a ficheros fuera de la carpeta estandar de Apache
	</Directory>
