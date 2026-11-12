**ELB y AutoEscalado**

Rafael Moreno Riquelme

2 FPS DAM

Índice

[1\. ELB (Balanceador de carga) 2](#1-elb-balanceador-de-carga)

[1.1 Crear Target Group 3](#11-crear-target-group)

[1.2 Crear Balanceador de Carga 6](#12-crear-balanceador-de-carga)

[2\. AutoEscalado 10](#2-autoescalado)

[2.1 Crear Launch Template 10](#21-crear-launch-template)

[2.2 Crear Auto Escalado 13](#22-crear-auto-escalado)

[3\. Comprobaciones 18](#3-comprobaciones)

# 1. ELB (Balanceador de carga)

## 1.1 Crear Target Group

![](ELB-y-AutoEscalado---Rafa-Moreno-images/image1.png)Accedemos a Grupos de destino/Target Group.

![](ELB-y-AutoEscalado---Rafa-Moreno-images/image2.png)Creamos una, en la configuración seleccionamos por **Instancias**.

![](ELB-y-AutoEscalado---Rafa-Moreno-images/image3.png)Añadimos un nombre, de protocolo mantenemos HTTP ya que no está configurado HTTPS.

![](ELB-y-AutoEscalado---Rafa-Moreno-images/image4.png)Seleccionamos la misma VPC en la que se encuentran las instancias.

![](ELB-y-AutoEscalado---Rafa-Moreno-images/image5.png)En el siguiente punto, seleccionamos las instancias a formar parte del grupo.

![](ELB-y-AutoEscalado---Rafa-Moreno-images/image6.png)Y seleccionamos "Incluir como pendiente a continuación".![](ELB-y-AutoEscalado---Rafa-Moreno-images/image7.png)

Y creamos el grupo de destino.

## 1.2 Crear Balanceador de Carga

![](ELB-y-AutoEscalado---Rafa-Moreno-images/image8.png)Seleccionamos "Balanceador de carga" en el menú de las instancias y pulsamos "Crear balanceador de carga".

![](ELB-y-AutoEscalado---Rafa-Moreno-images/image9.png)Seleccionamos un Balanceador de carga de aplicaciones (ALB), un balanceador por capa de aplicación, en este caso HTTP.

![](ELB-y-AutoEscalado---Rafa-Moreno-images/image10.png)Le asignamos un nombre y seleccionamos "Expuesto a internet".

![](ELB-y-AutoEscalado---Rafa-Moreno-images/image11.png)Seleccionamos la VPC de nuestras instancias.

![](ELB-y-AutoEscalado---Rafa-Moreno-images/image12.png)Y su zona de disponibilidad.

![](ELB-y-AutoEscalado---Rafa-Moreno-images/image13.png)Seleccionamos un grupo de seguridad que permita SSH (22), HTTP (80) y HTTPS (443).![](ELB-y-AutoEscalado---Rafa-Moreno-images/image14.png)

Añadimos el grupo de destino que hemos creado anteriormente y creamos el balanceador.![](ELB-y-AutoEscalado---Rafa-Moreno-images/image15.png)

# 2. AutoEscalado

## 2.1 Crear Launch Template

![](ELB-y-AutoEscalado---Rafa-Moreno-images/image16.png)Seleccionamos en la lista de opciones de EC2 "Plantillas de lanzamiento" y creamos una.

![](ELB-y-AutoEscalado---Rafa-Moreno-images/image17.png)Le asignamos un nombre y podemos seleccionar en "Orientación sobre Auto Scaling".

![](ELB-y-AutoEscalado---Rafa-Moreno-images/image18.png)Seleccionamos la imagen de máquina que teníamos previamente.

![](ELB-y-AutoEscalado---Rafa-Moreno-images/image19.png)Seleccionamos el mismo tipo de las otras instancias, en mi caso t3.small.

![](ELB-y-AutoEscalado---Rafa-Moreno-images/image20.png)En este caso le asignamos de par de claves "vockey" para poder acceder por SSH.

![](ELB-y-AutoEscalado---Rafa-Moreno-images/image21.png)Seleccionamos el mismo grupo de seguridad que las otras instancias.

![](ELB-y-AutoEscalado---Rafa-Moreno-images/image22.png)En datos de usuario añadimos el siguiente script para que se monte el EFS al iniciar la nueva instancia:

# !/bin/bash

\# Actualizar repositorios  
apt-get update –y  
\# Instalar cliente NFS  
apt-get install -y nfs-common  
\# Registrar montaje persistente de EFS  
echo "fs-02d5c2ffe7f94e598.efs.us-east-1.amazonaws.com:/ /var/www/html nfs4 defaults,\_netdev,nfsvers=4.1,rsize=1048576,wsize=1048576,hard,timeo=600,retrans=2,noresvport 0 0" >> /etc/fstab  
\# Montar EFS  
mount –a

Y creamos la plantilla de lanzamiento.

![](ELB-y-AutoEscalado---Rafa-Moreno-images/image23.png)

## 2.2 Crear Auto Escalado

![](ELB-y-AutoEscalado---Rafa-Moreno-images/image24.png)En las opciones de EC2 seleccionamos Grupos de Auto Scaling y creamos uno.

![](ELB-y-AutoEscalado---Rafa-Moreno-images/image25.png)Asignamos un nombre al grupo de auto escalado.

![](ELB-y-AutoEscalado---Rafa-Moreno-images/image26.png)Seleccionamos la plantilla de lanzamiento que creamos anteriormente.

![](ELB-y-AutoEscalado---Rafa-Moreno-images/image27.png)Seleccionamos la zona de disponibilidad de nuestras instancias

![](ELB-y-AutoEscalado---Rafa-Moreno-images/image28.png)Seleccionamos el balanceador de carga que habíamos creado anteriormente.

![](ELB-y-AutoEscalado---Rafa-Moreno-images/image29.png)En comprobaciones de estado seleccionamos "Activar las comprobaciones de estado de Elastic Load Balancing" para que si una instancia se encuentra en mal estado se sustituya.

![](ELB-y-AutoEscalado---Rafa-Moreno-images/image30.png)Seleccionamos el tamaño del grupo a 2, ya que tenemos dos instancias.

![](ELB-y-AutoEscalado---Rafa-Moreno-images/image31.png)En escalado, así como se indica seleccionamos mínimo 2 y máximo 5.

![](ELB-y-AutoEscalado---Rafa-Moreno-images/image32.png)Seleccionamos un escalado automático con política de seguimiento de destino. Utilizaremos como se pide el "promedio de la CPU" con valor de destino "60" y un tiempo de por ejemplo 300s.

Esto hará que si el promedio de CPU es el 60% se lanzaran nuevas instancias.

![](ELB-y-AutoEscalado---Rafa-Moreno-images/image33.png)En la configuración adicional, activamos la recopilación de métricas y de calentamiento predeterminado 300s. Esto permite ver cómo evoluciona el grupo en CloudWatch.

Y creamos el grupo de Auto Scaling.

![](ELB-y-AutoEscalado---Rafa-Moreno-images/image34.png)Se generarán dos instancias automáticas, las anteriores que eran manuales podemos eliminarlas.

# 3. Comprobaciones

Dentro de las dos instancias, vamos a lanzar los siguientes comandos:

**Primera instancia**:

stress-ng --cpu 4 --cpu-load 60 --timeout 400s

![](ELB-y-AutoEscalado---Rafa-Moreno-images/image35.png)

**Segunda instancia**:

stress-ng --cpu 4 --cpu-load 80 --timeout 400s

Para comprobar que si estresamos la CPU se generan nuevas instancias.

![](ELB-y-AutoEscalado---Rafa-Moreno-images/image36.png)

Tras algunos minutos el auto escalado está preparando nuevas instancias:![](ELB-y-AutoEscalado---Rafa-Moreno-images/image37.png)

Aquí ya las ha lanzado y solo prepara hasta 5:

![](ELB-y-AutoEscalado---Rafa-Moreno-images/image38.png)![](ELB-y-AutoEscalado---Rafa-Moreno-images/image39.png)

Una vez pasado el estrés tras un tiempo vuelven al mínimo de dos.

![](ELB-y-AutoEscalado---Rafa-Moreno-images/image40.png)