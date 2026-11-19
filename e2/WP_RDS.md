# Wordpress con RDS.

- Vamos a generar un Wordpress con la Base de Datos externalizada en una red privada.

## Creación del Wordpress.

> **Recuerda que puedes ayudarte de la práctica pasada de WP.**

- Durante la instalación ten en cuenta lo siguiente:

  🔥 **AMI**: Usar la última versión de WP del community marketplace.

  🔥 **IP elástica**.

  🔥 **Grupo de Seguridad**: Permite el tráfico SSH, HTTP y HTTPS.

## Creación de RDS.

> **Recuerda apuntarte la contraseña y el usuario de la base de datos.**

- Durante la instalación ten en cuenta lo siguiente:

  🔥 Crea una Base de Datos de **MariaDB**.

  🔥 Uso para **Entorno de Pruebas**.

  🔥 Usa el nombre para la instancia: BD-WP.

  🔥 Usuario y contraseña de la BD: admin / Usuario1234.

  🔥 **Grupo de Seguridad**: Permite el tráfico a 3306, pero solo desde origen del grupo de seguridad del wordpress.

  🔥 Sin IP pública.

## Configuración de la Base de Datos.

### Recabar el punto de enlace de la BD.

- Necesitamos recabar el punto de enlace para que el Wordpress pueda acceder a leer/escribir en la base de datos.

### Configuración Base de Datos.

> **Todos los comandos se hacen desde la EC2 de Wordpress.**

- Acceder a la gestión de la base de datos.

```bash
#Nos conectamos a la base de datos y nos pedirá la contraseña.
mysql -h "PUNTO_DE_ENLACE" -P 3306 -u admin -p
```

- Crear la Base de Datos.

> **Es importante hacer uso de ";" para que surja efecto cada comando.**

```sql
#Crear la base de datos.
CREATE DATABASE wordpress;
#Crear un usuario para gestionar esta base de datos.
#El usuario debe ser wp_user que es el que usa wordpress por defecto.
CREATE USER wp_user IDENTIFIED BY 'Usuario1234';
#Le damos privilegios al usuario.
GRANT ALL PRIVILEGES ON wordpress.* TO wp_user;
FLUSH PRIVILEGES;
```

### Configuración de Wordpress

- Vamos a cambiar la configuración de Wordpress para que el servicio apunte a la base de datos externa.

```bash
#Editamos el fichero de configuración.
sudo nano /bitnami/wordpress/wp-config.php
```

- Dentro del fichero de configuración debemos modificar los parámetros:

  🔥 DB_NAME: Nombre de base de datos.

  🔥 DB_USER: Nombre administrador de la base de datos.

  🔥 DB_PASSWORD: Contraseña del administrador de la base de datos.

  🔥 DB_HOST: Nombre de la máquina que contiene la base de datos.

> **Es muy mala práctica guardar la contraseña DB_PASSWORD en texto plano.**

### Instalación de Wordpress.

- Accede vía web como hiciste en la práctica anterior y termina de instalar el servicio de Wordpress.

### Comprobación.

Vamos a comprobar que efectivamente los datos se están almacenando en la RDS nueva externa que hemos creado.

- Accedemos al Wordpress y desde ahí ejecutamos:

```bash
#Nos conectamos a la base de datos y nos pedirá la contraseña.
mysql -h "PUNTO_DE_ENLACE" -P 3306 -u admin -p
#Accedemos a la base de datos de wordpress.
use database wordpress;
#Seleccionamos todos los usuarios de wordpress en su tabla por defecto wp_users.
select * from wp_users
```

- Aquí ya deberíamos de ver el usuario que hemos creado durante la instalación web de Wordpress.

## Vídeo Explicativo.

[Ver vídeo explicativo: WP RDS](https://www.youtube.com/watch?v=WUHzjhrS8tg)
