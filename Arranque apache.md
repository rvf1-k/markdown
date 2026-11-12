
```
#!/bin/bash

#Actualizar repositorios
apt-get update -y

#Instalar Apache
apt-get install -y apache2

#Arrancar Apache y hacer que se inicie automáticamente
systemctl enable apache2
systemctl start apache2
```