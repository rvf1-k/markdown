# AWS RDS

## ¿Qué es RDS?

**Amazon RDS (Relational Database Service)** es un servicio administrado de AWS para utilizar bases de datos relacionales sin tener que instalar ni mantener manualmente el servidor y el SGBD.

- Incluye el software de base de datos instalado y configurado.
- Es un servicio **PaaS**: AWS se encarga de gran parte del mantenimiento.
- SGBD compatibles: **MariaDB, PostgreSQL, MySQL** y otros.
- **Amazon Aurora** es un motor de base de datos de AWS compatible con MySQL y PostgreSQL, optimizado para AWS.

También podríamos instalar un SGBD en una instancia EC2, pero entonces tendríamos que encargarnos de su instalación, actualizaciones, copias de seguridad, disponibilidad y escalabilidad.

## Seguridad

- Lo habitual es desplegar RDS en una **VPC privada**, sin acceso directo desde Internet.
- Solo deben poder conectarse las EC2 o servicios que necesiten acceder a la base de datos.
- Se controla el acceso mediante la configuración de red y las reglas de seguridad.

## Alta disponibilidad y copias

### Multi-AZ

Mantiene una copia de respaldo de la base de datos en **otra zona de disponibilidad**.

- Replicación **síncrona**: los cambios se escriben en la principal y en la copia.
- Si falla la instancia principal, AWS realiza el cambio a la instancia de respaldo **automáticamente**.
- Se utiliza cuando no podemos permitirnos perder transacciones, por ejemplo, en sistemas financieros o tiendas online.

### Réplica de lectura (Read Replica)

Crea una copia de la base de datos que puede utilizarse para repartir las consultas de lectura.

- Puede estar en otra zona de disponibilidad o región.
- Replicación **asíncrona**: los cambios llegan a la réplica con cierto retraso.
- Si falla la base de datos principal, la promoción de la réplica se realiza **manualmente**.
- Es útil para aumentar la capacidad de lectura y para escenarios donde no se necesita consistencia inmediata.

### Instantáneas (Snapshots)

Son copias de seguridad de la base de datos en un momento concreto.

- Se pueden crear manualmente o programar.
- Según el material, por defecto se generan diariamente y se conservan durante 7 días.
- Se pueden almacenar en otras regiones.
- El material advierte de que su creación puede afectar temporalmente a la actividad del disco; conviene programarlas en periodos de poca carga.

## Costes

Los principales factores que influyen en el precio de RDS son:

- **Tiempo de funcionamiento** de la instancia.
- **Tipo de instancia**: CPU, RAM y almacenamiento.
- **Almacenamiento** utilizado.
- Funciones adicionales: **Multi-AZ, réplicas e instantáneas**.
- Operaciones o transacciones realizadas, según el motor y la modalidad de cobro.

> Una transacción es una operación de lectura o escritura en la base de datos.

## Comparación rápida: Multi-AZ vs. Read Replica

| Característica | Multi-AZ | Read Replica |
|---|---|---|
| Objetivo principal | Alta disponibilidad | Escalar lecturas |
| Replicación | Síncrona | Asíncrona |
| Ubicación | Otra zona de disponibilidad | Otra AZ o región |
| Cambio ante fallo | Automático | Manual |
| ¿Puede haber retraso? | No se plantea como replicación asíncrona | Sí |

## Lo que debes memorizar

- **RDS = base de datos relacional administrada (PaaS).**
- **Multi-AZ = síncrona + conmutación automática + alta disponibilidad.**
- **Read Replica = asíncrona + promoción manual + repartir lecturas.**
- **Snapshot = copia de seguridad en un momento concreto.**
- **Seguridad = normalmente dentro de una VPC privada.**
- **Aurora = motor de AWS compatible con MySQL y PostgreSQL.**

---

Fuente: [RDS — Introducción a la Nube Pública](https://salvasevero.github.io/cloudGS/17_RDS.html)  
Material de Salvador Serrano Pérez.
