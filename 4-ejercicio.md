## Esquema para el ejercicio
![Imagen](esquema-4-ejercicio.PNG)

### Crear la red
```
docker network create net-wp -d bridge
```

### Crear el contenedor mysql a partir de la imagen mysql:8, configurar las variables de entorno necesarias
```
docker run -d --name contenedor-mysql --network net-wp -e MYSQL_ROOT_PASSWORD=rootpass -e MYSQL_DATABASE=wordpress -e MYSQL_USER=wpuser -e MYSQL_PASSWORD=wppass mysql:8
```
**Variables de entorno configuradas:**
- `MYSQL_ROOT_PASSWORD`: rootpass
- `MYSQL_DATABASE`: wordpress
- `MYSQL_USER`: wpuser
- `MYSQL_PASSWORD`: wppass


### Crear el contenedor wordpress a partir de la imagen: wordpress, configurar las variables de entorno necesarias
```
docker run -d --name contenedor-wordpress --network net-wp -p 9300:80 -e WORDPRESS_DB_HOST=contenedor-mysql:3306 -e WORDPRESS_DB_NAME=wordpress -e WORDPRESS_DB_USER=wpuser -e WORDPRESS_DB_PASSWORD=wppass wordpress
```
**Variables de entorno configuradas:**
- `WORDPRESS_DB_HOST`: contenedor-mysql:3306 (nombre del contenedor MySQL)
- `WORDPRESS_DB_NAME`: wordpress
- `WORDPRESS_DB_USER`: wpuser
- `WORDPRESS_DB_PASSWORD`: wppass

**Verificar que ambos contenedores estén corriendo:**
```bash
docker ps
```
<img width="1778" height="161" alt="image" src="https://github.com/user-attachments/assets/8f6cfa9e-9dc2-4181-b6fe-ef8c4effa05b" />


De acuerdo con el trabajo realizado, en el esquema del ejercicio el puerto a es **(completar con el valor)**

Ingresar desde el navegador al wordpress y finalizar la configuración de instalación.
# COLOCAR UNA CAPTURA DE LA CONFIGURACIÓN
<img width="1919" height="678" alt="image" src="https://github.com/user-attachments/assets/9143c71d-c37a-48ee-a027-7db7041c762e" />

Desde el panel de admin: cambiar el tema y crear una nueva publicación.
Ingresar a: http://localhost:9300/ 
recordar que a es el puerto que usó para el mapeo con wordpress
# COLOCAR UNA CAPTURA DEL SITO EN DONDE SEA VISIBLE LA PUBLICACIÓN.
<img width="1919" height="970" alt="image" src="https://github.com/user-attachments/assets/7ac20058-4070-41cf-bb60-857b27bb19cf" />
<img width="1913" height="587" alt="image" src="https://github.com/user-attachments/assets/5dda2f7a-c606-4ac1-8903-6bf8517fba66" />


### Eliminar el contenedor wordpress
```
docker stop contenedor-wordpress
docker rm contenedor-wordpress
```

### Crear nuevamente el contenedor wordpress
Ingresar a: http://localhost:9300/ 
recordar que a es el puerto que usó para el mapeo con wordpress
```
docker run -d --name contenedor-wordpress --network net-wp -p 9300:80 -e WORDPRESS_DB_HOST=contenedor-mysql:3306 -e WORDPRESS_DB_NAME=wordpress -e WORDPRESS_DB_USER=wpuser -e WORDPRESS_DB_PASSWORD=wppass wordpress
```

### ¿Qué ha sucedido, qué puede observar?
Al acceder nuevamente a `http://localhost:9300/`, se puede observar que:

1. **El sitio WordPress solicita nuevamente la configuración inicial** (idioma, instalación, etc.)
2. **Se ha perdido toda la configuración anterior**: el tema personalizado, las publicaciones creadas, usuarios, y cualquier cambio realizado
3. **Solo persisten los datos en la base de datos MySQL**: Como el contenedor MySQL no fue eliminado, la base de datos `wordpress` aún existe con todos sus datos

**Razón del problema:**
Los archivos de WordPress (temas, plugins, uploads, configuraciones) se almacenan en el sistema de archivos del contenedor. Al eliminar el contenedor WordPress, **todos estos archivos se pierden** porque los contenedores son efímeros por naturaleza.

**Solución:**
Para persistir los datos de WordPress entre reinicios del contenedor, se deben usar **volúmenes de Docker**
