
Este repositorio tiene dos objetivos: 

	1- Generación de una imagen base con:
	    - Ubuntu 18.04
	    - Apache 2.2
	    - PHP 7.3.11
	2- Levantar 3 dockers con:
	    - Contenedor Web con la imagen antes creada de Ubuntu+Apache+PHP
	    - Contenedor MySQL 5.7
	    - Contenedor PhpMyAdmin

<b>Generación de la imagen web </b>

Esta ejecucion crea una imagen con Unutnu 18.04 y PHP 7.3.11

(1) Primer paso ejecutar el dockerfile de /Dockerfile

para ello nos situacmo en /Dockerfile
y ejecutamos:

	lanza-dockerfile


Después de ejecutar el dockerfile se creará la siguiente imagen:

	vicsoft01/ubuntu18.04-apache2.2-php7.3.11

para lanzar el docker de esta imagen deben existir los siguentes direcorios o bien personalizarlos y modificarlos en el archivo: lanza

	/home/nuevatienda/luigiz.com/tienda/prod-web/prestashop

luego ejecutar:

	docker run -dti -p 80:80 -v /home/nuevatienda/luigiz.com/tienda/prod-web/prestashop:/var/www/html  --name tienda -h vicsoft vicsoft01/ubuntu18.04-apache2.2-php7.3.11


esta imagen no contien MySQL por lo que se va a guardar en DockerHub para, sobre ella, instalar Mysql

para commitear el contenedor buscamos su container id :

[root@antserver Base-Ubuntu-18.04-Apache2.2-PHP-7.3.11]# docker ps -a
CONTAINER ID        IMAGE                                       COMMAND                  CREATED             STATUS                   PORTS                NAMES
16a5580e70c5        vicsoft01/ubuntu18.04-apache2.2-php7.3.11   "/ejecuta.sh"            13 minutes ago      Up 13 minutes            0.0.0.0:80->80/tcp   tienda

luego hacemos push

	docker push vicsoft01/ubuntu18.04-apache2.2-php7.3.11


esta será la imagen base con ubuntu, apache y PHP


* Problemas detectados

- durante la instalación de la tienda prestashop, aparecía un error de timeou de nginx durante la creación de las tablas de bbdd.

La solución ha sido, meterme en el contenedor de nginx, y hacer lo siguiente:

1. Accedemos el directorio «/etc/nginx/conf.d

 cd /etc/nginx/conf.d

2. Creamos un fichero cualquiera *.conf. Por ejemplo, timeout.conf. El fichero nginx.conf se encargará de importarlo automáticamente.

 vi timeout.conf

3. Lo editamos, escribiendo las siguientes líneas:

proxy_connect_timeout 600;
proxy_send_timeout 600;
proxy_read_timeout 600;
send_timeout 600;

luego he hecho un commit del contendor del ngix 
y lo he subido a dockerhub

luego he modificado el docker-compose.yml para cambiar la anterior imagen de ngix por la nueva que he subido a docker-hub

luego he vuelto a ejecutar el compose y a reiniciar la instalación de prestashop, funcionando perfectamente.

# PONER EN MARCHA LA TIENDA
Crear dos directorios:
 - uno donde residirá la web
 - otro donde residirá la bbdd
 
ambos direcorios deben ser modificados en los valores -v del archivo docker-compose.yml

en el directorio de web:
	git clone https://github.com/usuarioregalonatural/tienda-base-prestashop-1.7.6.1.git

en el directorio de BBDD:
	git clone https://github.com/usuarioregalonatural/tienda-base-prestashop-1-7-6-1-BBDD.git

con esto bastaría para poder ejecutar una tienda Prespashop en blanco.

