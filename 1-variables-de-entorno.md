# Variables de Entorno
### ¿Qué son las variables de entorno?
Las variables de entorno son valores dinámicos que afectan el comportamiento de los procesos en un sistema operativo o contenedor. En Docker, permiten configurar aplicaciones sin modificar el código, pasando parámetros como credenciales, rutas, puertos y otras configuraciones al momento de crear o ejecutar un contenedor.

### Para crear un contenedor con variables de entorno

```
docker run -d --name <nombre contenedor> -e <nombre variable1>=<valor1> -e <nombre variable2>=<valor2>
```

### Crear un contenedor a partir de la imagen de nginx:alpine con las siguientes variables de entorno: username y role. Para la variable de entorno rol asignar el valor admin.
```
docker run -d --name nginx-con-variables -e username=usuario1 -e role=admin nginx:alpine
```

# CAPTURA CON LA COMPROBACIÓN DE LA CREACIÓN DE LAS VARIABLES DE ENTORNO DEL CONTENEDOR ANTERIOR
<img width="805" height="264" alt="image" src="https://github.com/user-attachments/assets/d04d05f8-934a-49b4-873a-76f6ef6fc310" />
<img width="1246" height="321" alt="image" src="https://github.com/user-attachments/assets/1f8b9a97-c556-4f8c-a5e7-a61bbf4a38ed" />

### Crear un contenedor con la imagen de mysql, mapear todos los puertos
```
docker run -d --name mysql-container -P mysql
```

### ¿El contenedor se está ejecutando?
No, ya que podemos ver el estado del contenerdor y esta en estado EXITED
<img width="1576" height="121" alt="image" src="https://github.com/user-attachments/assets/92d5c6aa-ea93-4a1a-a4b5-40e07902405a" />


### Identificar el problema
El problema es que MySQL requiere que se establezcan variables de entorno obligatorias para su inicialización. Específicamente, necesita al menos una de las siguientes variables:
- `MYSQL_ROOT_PASSWORD`: Contraseña para el usuario root
- `MYSQL_ALLOW_EMPTY_PASSWORD`: Permite contraseña vacía (no recomendado)
- `MYSQL_RANDOM_ROOT_PASSWORD`: Genera una contraseña aleatoria
Para solucionarlo, creamos el contenedor con las variables requeridas, previamente eliminamos el contenedor anterior con el comando:
```
docker rm mysql-container
```
Y para crear.
```
docker run -d --name mysql-container -P -e MYSQL_ROOT_PASSWORD=pass123 mysql
```

### Para crear un contenedor con variables de entorno especificadas
- Portabilidad: Las aplicaciones se vuelven más portátiles y pueden ser desplegadas en diferentes entornos (desarrollo, pruebas, producción) simplemente cambiando el archivo de variables de entorno.
- Centralización: Todas las configuraciones importantes se centralizan en un solo lugar, lo que facilita la gestión y auditoría de las configuraciones.
- Consistencia: Asegura que todos los miembros del equipo de desarrollo o los entornos de despliegue utilicen las mismas configuraciones.
- Evitar Exposición en el Código: Mantener variables sensibles como contraseñas, claves API, y tokens fuera del código fuente reduce el riesgo de exposición accidental a través del control de versiones.
- Control de Acceso: Los archivos de variables de entorno pueden ser gestionados con permisos específicos, limitando quién puede ver o modificar la configuración sensible.

### ¿Qué bases de datos existen en el contenedor creado?
Para listar las bases de datos, conectarse al contenedor MySQL:
```
docker exec -it mysql-container mysql -uroot -ppass123
```
Dentro del cliente MySQL, ejecutar:
```
SHOW DATABASES;
```
<img width="1122" height="751" alt="image" src="https://github.com/user-attachments/assets/003012b7-935b-4b52-956a-34529ae4f2b0" />
