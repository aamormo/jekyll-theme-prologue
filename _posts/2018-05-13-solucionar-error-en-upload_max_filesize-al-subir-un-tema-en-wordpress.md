---
title: Solucionar error en upload_max_filesize al subir un tema en WordPress
layout: post
---

# Solucionar error en upload_max_filesize al subir un tema en WordPress

Por la configuración por defecto en la instalación de PHP, el servidor Apache cuenta con una restricción de 2M en cuanto al peso de los ficheros que se pueden subir, si intentamos subir un tema de WordPress sin editar esa restricción, seguramente aparecera una mensaje diciendo:

El archivo subido excede la directiva upload_max_filesize en php.ini.

con lo que es recomendable editar dicha restricción, para evitar posibles errores, y así poder subir los temas personalizados a WordPress. De manera general esa restricción se cambia en el fichero php.ini de la instalación de PHP, pero la manera de editarlo depende de si la instalación esta en local o en producción

## Instalación en local
+ ir al directorio donde se tenga instalado PHP

+ localizar el fichero php.ini y editarlo

+ usar la conbinación de teclas Ctrl + F y escribir en el buscador la instrucción upload_max_filesize

+ editar la cifra, por ejemplo, a 10M

+ guardar y reiniciar el Apache

## Instalación en producción
+ iniciar sesión en PuTTY

+ ejecutar locate php.ini

+ nos dara como resultado varios ficheros, como se puede ver en la imagen

[img/locate-resultado.png]

+ buscaremos y editaremos el siguiente:

/etc/php/7.0/apache2/php.ini

+ usar la conbinación de teclas Ctrl + F y escribir en el buscador la instrucción upload_max_filesize

+ editar la cifra, por ejemplo, a 10M

+ guardar y reiniciar el Apache con service apache2 restart

Una vez termiando el proceso, debemos ir a WordPress entrando con el navegador, y después ir a Apariencia > Temas

Clickaremos en la opción Añadir nuevo, localizada en la parte superior, y después en Subir tema, localiada en el mismo sitio

En la nueva opción que aparece, clickaremos en el botón Examinar, navegaremos hasta donde tengamos guardado el archivo .zip con el tema, y lo seleccionaremos para subirlo

Una vez subido, aparecera su nombre y un botón que dice Instalar ahora, haremos click sobre el

Se iniciara un proceso, en el que si todo va bien, finalizara diciendo que el tema esta instalado y dándonos la opción de activarlo

[img/subit-tema-ok.png]

daremos click en el enlace Activar, y nos aparecera como activo junto al resto de temas y pudiendo personalizarlo
