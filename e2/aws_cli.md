# AWS CLI.

## ¿Qués es AWS CLI?

AWS CLI (Command Line Interface) es una herramienta que se ejecuta desde la línea de comandos que permite `gestionar servicios` de Amazon Web Services, es decir, crear y gestionar EC2, S3, EFS, EBS, etc a través de comandos.

## Instalación.

[Descarga AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html)

## Configuración.

- Abrimos un terminal y ejecutamos `aws configure`

> **El comando nos preguntará por información, la cual podemos dejar en blanco para el caso de AWS Academy.**

```text
AWS Access Key ID [None]:
AWS Secret Access Key [None]:
Default region name [None]:
Default output format [None]:
```

- Esto crea el fichero `.aws/credentials`, que editaremos.

> **Credentials**
>
> - En Linux/macOs el archivo estará en la ruta: `~/.aws/credentials`.
> - En Windows estará en la ruta: `C:\Users\usuario\.aws\credentials`.

- Dentro de ➡️ AWS Details ➡️ Cloud Access ➡️ AWS CLI podemos recabar la información necesaria:

> **Solo es necesario copiar y pegar todo el texto en el fichero credentials.**

- Una vez editado el fichero con la nueva información ejecutamos de nuevo `aws configure`:

  💥 Región: us-east-1

  💥 Formato: json

> **En el caso del AWS Academy deberemos editar dicha información cada vez que arranquemos el Laboratorio.**

- Podemos comprobar el usuario con el que estamos registrados con `aws sts get-caller-identity`.

## Creación de EC2.

```bash
aws ec2 run-instances \
    --image-id ami-0360c520857e3138f \
    --count 1 \
    --instance-type t2.micro \
    --key-name vockey \
    --security-groups PermiteSSH \
    --user-data "sudo apt update && sudo apt install -y nginx" \
    --tag-specifications 'ResourceType=instance,Tags=[{Key=Name,Value=mi-servidor-web}]'
```

- **image-id**: El ID de la imagen AMI.

> **No todas las AMI están disponibles en todas las regiones.**

- **count**: Número de instancias a crear.

- **instance-type**: Tipo de máquina (CPU / RAM)

- **key-name**: Claves de acceso ssh (vockey).

- **security-groups**: Filtros de seguridad que aplicas.

- **user-data**: Comandos a ejecutar al crear la máquina.

  🔥 Se le puede pasar un script directamente: `--user-data file://instalar-nginx.sh`

- **tag-specifications**: Se usa para información de etiquetas como el nombre de la EC2 (mi-servidor-web).

> **Comando de ayuda: `aws ec2 run-instances help`.**

## Grupos de Seguridad.

Vamos a crear una regla que permita el tráfico de entrada para SSH(22), HTTP(80), HTTPS(443).

#### Primero creamos el grupo de seguridad que incluirá todas las reglas.

```bash
aws ec2 create-security-group \
    --group-name permite-web \
    --description "Permite acceso ssh y web"
```

#### Añadimos regla de entrada que permita el SSH.

```bash
aws ec2 authorize-security-group-ingress \
    --group-name permite-web \
    --protocol tcp \
    --port 22 \
    --cidr 0.0.0.0/0
```

#### Añadimos regla de entrada que permita el HTTP.

```bash
aws ec2 authorize-security-group-ingress \
    --group-name permite-web \
    --protocol tcp \
    --port 80 \
    --cidr 0.0.0.0/0
```

#### Añadimos regla de entrada que permita el HTTPS.

```bash
aws ec2 authorize-security-group-ingress \
    --group-name permite-web \
    --protocol tcp \
    --port 443 \
    --cidr 0.0.0.0/0
```

- **aws ec2 authorize-security-group-ingress**: Crea una regla de seguridad de entrada sobre el tráfico entrante (ingress) a tu máquina.

- **group-name**: Nombre del grupo de seguridad al que se le añade la regla.

- **cidr**: Direcciones IP o Red a la que afecta la regla.

> **El CIDR 0.0.0.0/0 indica cualquier dirección IP.**

## Creación de una AMI.

```bash
#Crear imagen de una instancia con AWS-CLI o CloudShell.
aws ec2 create-image --instance-id INSTANCIA_EC2 --name NOMBRE_AMI
```

## Gestión S3.

#### Crea un cubo S3.

```bash
aws s3 mb s3://mibucket-unico-total
```

> **Recuerda que los nombres de cubos deben ser únicos en todo AWS.**

#### Lista los cubos existentes.

```bash
aws s3 ls
```

**¿Cómo listarías el contenido de un cubo?**

```bash
aws s3 ls s3://mibucket-unico-total
```

#### Subir un fichero.

```bash
aws s3 cp labS3.csv s3://mibucket-unico-total
```

**¿Cómo descargarías un fichero desde el S3?**

```bash
aws s3 cp s3://mibucket-unico-total/labS3.csv copiaLabS3.csv
```

#### Subir una carpeta.

```bash
aws s3 sync nombreCarpeta s3://mibucket-unico-total
```

#### Borrar un Bucket S3.

```bash
aws s3 rb --force s3://mibucket-unico-total
```

**¿Cómo eliminarías un fichero de un cubo?**

```bash
aws s3 rm s3://mibucket-unico-total/Fichero
```

#### Activar modo web estática

```bash
aws s3 website s3://mibucket-unico-total/ \
  --index-document index.html \
  --error-document error.html
```

> **El error-document no es obligatorio.**

### s3api:

El comando s3 se usa para la gestión de los objetos mayormente, mientras que para la gestión del servicio s3 se usa el comando s3api:

- Desactivar el **bloqueo de acceso público**.
- Aplicar una **política** de bucket (bucket policy).
- Avtivar el **versionado**.

> **Para todo eso necesitas aws s3api.**

#### Hacer búsquedas de archivos filtradas.

```bash
aws s3api list-objects --bucket mibucket-unico-total --prefix "nombre"
```

#### Activar versionado.

```bash
aws s3api put-bucket-versioning --bucket mibucket-unico-total --versioning-configuration Status=Enabled
```

#### Habilitar el acceso público.

```bash
aws s3api put-public-access-block \
  --bucket mibucket-unico-total \
  --public-access-block-configuration \
  "BlockPublicAcls=false,IgnorePublicAcls=false,BlockPublicPolicy=false,RestrictPublicBuckets=false"
```

#### Crear políticas.

```bash
aws s3api put-bucket-policy --bucket mibucket-unico-total --policy file://PermiteLectura.json
```

```json
//PermiteLectura.json
{
    "Version": "2026-10-17",
    "Statement": [{
      "Sid": "PermiteLectura",
      "Effect": "Allow",
      "Principal": "*",
      "Action": "s3:GetObject",
      "Resource": "arn:aws:s3:::mibucket-unico-total/*"
    }]
}
```

### Resumen.

| Operación | Comando |
|---|---|
| `Crear un bucket` | aws s3 mb |
| `Listar buckets` | aws s3 ls |
| `Subir y descargar objetos` | aws s3 cp |
| `Sincronizar carpetas` | aws s3 sync |
| `Eliminar objetos` | aws s3 rm |
| `Alojamiento web estático` | aws s3 website |
| `Activar versionado` | aws s3api put-bucket-versioning |
| `Bucket policy` | aws s3api put-bucket-policy |
| `Bloqueo de acceso público` | aws s3api put-public-access-block |
