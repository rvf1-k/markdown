**Web Estática Práctica**

Rafael Moreno Riquelme

2 FPS DAM

Índice

[1\. Establece la página estática 2](#1-establece-la-página-estática)

[1.1 Crear S3 3](#11-crear-s3)

[1.2 Configura el sitio web estático 4](#12-configura-el-sitio-web-estático)

[1.3 Subir web al S3 4](#13-subir-web-al-s3)

[2\. Crear política del bucket 6](#2-crear-política-del-bucket)

[3\. Gestionar versiones 7](#3-gestionar-versiones)

[4\. Crear replica de almacenamiento en otra región 9](#4-crear-replica-de-almacenamiento-en-otra-región)

[4.1 Crear S3 auxiliar 9](#41-crear-s3-auxiliar)

[4.2. Replicar S3 10](#42-replicar-s3)

[4.3 Comprobar replica 13](#43-comprobar-replica)

[4.3.1 Modificar el archivo 13](#431-modificar-el-archivo)

[4.4 Comprobar cambios 15](#44-comprobar-cambios)

# 1. Establece la página estática

## 1.1 Crear S3

Ponemos un nombre único:

![](Web-Estática-Práctica---Rafa-Moreno-images/image1.png)

Configuramos el tráfico público:

![](Web-Estática-Práctica---Rafa-Moreno-images/image2.png)

Habilitamos el control de versiones:

![](Web-Estática-Práctica---Rafa-Moreno-images/image3.png)

## 1.2 Configura el sitio web estático

![](Web-Estática-Práctica---Rafa-Moreno-images/image4.png)

Ahora podemos acceder a la web estática desde el enlace:

## ![](Web-Estática-Práctica---Rafa-Moreno-images/image5.png)1.3 Subir web al S3

En los objetos del S3 pulsamos cargar un objeto:

![](Web-Estática-Práctica---Rafa-Moreno-images/image6.png)

Y cargamos la carpeta de la web (Este contenido se ha proporcionado a través de aules en <https://aules.edu.gva.es/fp/mod/resource/view.php?id=11708787>)

![](Web-Estática-Práctica---Rafa-Moreno-images/image7.png)

# 2. Crear política del bucket

Dentro del bucket en Permisos, pulsa editar la Política de bucket.

Pulsamos en "**Agregar nueva instrucción**" y se creará una plantilla para el permiso, en campo de versión no es la versión de la política, es un identificador de versión del lenguaje de políticas de IAM existiendo "2012-10-17" y "2008-10-17" siendo esta la antigua.

En Principal podemos quitar las llaves y añadir "\*", que quiere decir que todo puede acceder, es un permiso a todos los usuarios.

Ahora vamos a añadir la acción, dentro del panel de la izquierda buscamos el servicio s3 y buscamos la acción getObject, dentro del array de action quedaría tal que así "s3:GetObject".

Por último, en Resources, indicamos como único valor el ARN del bucket (se muestra arriba) con "/\*" para indicar que hacemos referencia a todos los objetos del bucket, quedando tal que así: "Resource": "arn:aws:s3:::web-estatica-nube-rafa/\*"

# 3. Gestionar versiones

Se necesita crear una regla del ciclo de vida para que si un archivo versionado antiguo tiene más de 100 días y no es la última versión generada se elimina automáticamente.

![](Web-Estática-Práctica---Rafa-Moreno-images/image8.png)

Creamos una regla, le añadimos un nombre e indicamos que se aplique a todos los objetos del bucket.

![](Web-Estática-Práctica---Rafa-Moreno-images/image9.png)

Y en las acciones seleccionamos "Eliminar permanentemente las versiones no actuales de los objetos", añadimos de días 100 y como número de versiones recientes a mantener 1. Para que siempre se quede la última versión más reciente.

![](Web-Estática-Práctica---Rafa-Moreno-images/image10.png)

# 4. Crear replica de almacenamiento en otra región

## 4.1 Crear S3 auxiliar

Cambiamos la región de AWS, en este caso a Oregon.

![](Web-Estática-Práctica---Rafa-Moreno-images/image11.png)  
Volvemos a la pestaña de crear bucket visto en el aparatado 1.1 Crear S3 para crear un nuevo S3 con la misma configuración del anterior.

![](Web-Estática-Práctica---Rafa-Moreno-images/image12.png)

## 4.2. Replicar S3

Dentro del primer bucket añadimos una regla de replicación.

![](Web-Estática-Práctica---Rafa-Moreno-images/image13.png)

Añadimos un nombre y debe estar establecido como habilitada.![](Web-Estática-Práctica---Rafa-Moreno-images/image14.png)

Aplicamos a todos los objetos del bucket:

![](Web-Estática-Práctica---Rafa-Moreno-images/image15.png)

Incidamos como destino el bucket auxiliar, que funcionará como una réplica.![](Web-Estática-Práctica---Rafa-Moreno-images/image16.png)

Indicamos el rol de IAM en este caso LabRole. ![](Web-Estática-Práctica---Rafa-Moreno-images/image17.png)

Pulsamos crear e importante indicar que no se repliquen los objetos existentes. Para que así a partir de ahora, solo los elementos modificados sean replicados.

![](Web-Estática-Práctica---Rafa-Moreno-images/image18.png)

## 4.3 Comprobar replica

## 4.3.1 Modificar el archivo

Vamos a los objetos de la S3 original y modificamos un archivo, en este caso el index.html.

![](Web-Estática-Práctica---Rafa-Moreno-images/image19.png)

Añadimos una línea nueva:

![](Web-Estática-Práctica---Rafa-Moreno-images/image20.png)

Y cargamos el nuevo index.html en el directorio del S3 original.

![](Web-Estática-Práctica---Rafa-Moreno-images/image21.png)

## 4.4 Comprobar cambios

Dentro del S3 original se ha actualizado el index.html.

![](Web-Estática-Práctica---Rafa-Moreno-images/image22.png)

Si comprobamos el directorio del S3 auxiliar se ha añadido nuestro index.html, con la modificación.

![](Web-Estática-Práctica---Rafa-Moreno-images/image23.png)

![](Web-Estática-Práctica---Rafa-Moreno-images/image24.png)