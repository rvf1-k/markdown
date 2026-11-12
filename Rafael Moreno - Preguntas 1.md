**Preguntas Sesión 1. Introducción a la nube.**

**En base al ejemplo guiado de la calculadora de costes de AWS (**[**https://calculator.aws**](https://calculator.aws)**) visto en clase, razona y responde la siguientes preguntas con tus propias palabras.**

- _¿Qué diferencia hay entre una instancia compartida y una instancia dedicada?_

_En una_ **_i_**_nstancia compartida (~1.53 USD) corren instancias de distintos clientes en un mismo servidor, al contrario que en una instancia dedicada_ en la que se usa un único servidor. La mayor diferencia en la práctica es la seguridad y la estabilidad que aporta un servidor dedicado, pero con un precio más elevado (~ 1,463.80 USD).

- _¿Tiene el mismo precio un mismo servicio de AWS independientemente de la Región que elijamos?_

No, debido a la oferta de servidores en una región, a la hora de seleccionar regiones hay que tener en cuenta el posible _target_, ya que podría tener problemas de latencia si el usuario está lejos de esa región.

- _¿Existen los mismos servicios AWS en todas las regiones?_

No, no todas las regiones tienen porque tener los mismos tipos de servidores, por lo tanto, pueden no ofrecer los mismos servicios.

- _¿Si queremos levantar una tienda online, podemos crearla en una instancia spot? ¿Por qué?_

No, este tipo de instancias son puntuales y marcadas por horas de funcionamiento, tienen un precio más reducido, 90% en comparación a bajo demanda, se recomienda para aplicaciones que pueden tolerar interrupciones.

- _¿Se paga por uso del tráfico que nos llega nuestras máquinas virtuales?_

No.

- _¿Se paga por el tráfico interno entre nuestros servicios?_

No, exepto si están en Zonas de disponibilidad distintas.

- _¿Se paga por el tráfico que sale de nuestras máquinas virtuales hacia fuera de AWS (Internet)?_

Si.

- _¿Qué significa cuando por ejemplo en una Base de Datos elegimos que venga incorporado con el servicio MultiAZ?_

Significa que tu Base de Datos se puede replicar en centro de datos distintos de la misma región.

- _Explica qué son los siguientes servicios de AWS: EC2, RDS, CloudFront, S3. ¿Cuáles son sus equivalentes en la plataforma de Azure?_

**_EC2 (Elastic Compute Cloud)_**

Este servicio permite alquilar máquinas virtuales. Su alternativa en Azure es **Azure Virtual Machines**.

**Amazon RDS (Relational Database Service)**

Este servicio ofrece una base de datos relación. Su alternativa en Azure es **Azure SQL Database** aunque tiene para otros motores como PostgreSQL y MySQL.

**CloudFront**

Es una Red de Entrega de Contenido (CDN), distribuye datos, videos, aplicaciones y APIs. Azure tiene nos alternativas Azure CDN (Content Delivery Network) o Azure Front Door, siento este último una versión moderna.

**S3 (Simple Storage Service)**

Es un servicio de almacenamiento para volúmenes de datos no estructurados. Su alternativa en Azure es **Azure Blob Storage**.

- _Tienes una tienda online y creas una máquina virtual para alojarla en AWS, tú como administrador te encargarás de instalar y configurar el software y servicio dentro de dicha máquina. ¿De qué tipo de servicio se trata IaaS, PaaS o SaaS? Razona tu respuesta._

IaaS (Infrastructure as a Service), lo único que no hay que administrar es el hardware.

- _Trabajas en una empresa de desarrollo de software y levantas un contenedor o máquina virtual con todas las librerías, aplicaciones, dependencias, actualizaciones, etc ya configuradas en él, preparadas para que los desarrolladores de código se centren únicamente en programar. ¿Qué tipo de servicio estás levantando: IaaS, PaaS o SaaS? Razona tu respuesta._

_PaaS (Plataform as a Service), es perfecta para empezar a desarrollar al momento._

- _Mantienes una tienda online pequeña de una panadería, únicamente tiene unos pocos clientes por la web ¿Crees que merecería la pena configurar dicho servicio en una plataforma en la nube como AWS, Azure, Google Cloud? ¿Por qué?_

No, no sería rentable.