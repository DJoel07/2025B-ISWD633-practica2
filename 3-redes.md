# Redes
Las redes son un componente fundamental que permite la comunicación entre contenedores, así como la comunicación de los contenedores con el mundo exterior. 
![Imagen](redes.PNG)
- Bridge: Esta es la red por defecto en Docker. Permite la comunicación entre contenedores en el mismo host. Cada contenedor conectado a la red bridge tiene una IP propia en la subred de la red bridge.
    -  Brige por default: Cuando se ejecuta un contenedor, Docker crea automáticamente una red de tipo bridge por default. Esta red se utiliza para permitir la comunicación entre contenedores en el mismo host. Cada contenedor conectado a esta red obtiene su propia dirección IP en la subred de la red bridge.
    - Bridge creada por nosotros: Un usuario también puede crear sus propias redes de tipo bridge en Docker. Esto puede ser útil para organizar y segmentar los contenedores de una aplicación de manera más controlada. Al crear una red bridge personalizada, se puede especificar un rango de direcciones IP y otras configuraciones de red específicas. Los contenedores conectados a esta red utilizarán las direcciones IP de la subred definida por el usuario.
- Host: Con esta red, los contenedores comparten la red del host en lugar de tener su propia interfaz de red. Esto puede mejorar el rendimiento de red, pero los contenedores pueden entrar en conflicto con los puertos del host si intentan utilizar los mismos puertos.
- None: Con esta red, se deshabilita la configuración de red. Los contenedores que usan esta red tienen su propia red de loopback y no pueden comunicarse con otros contenedores a menos que se conecten explícitamente a una red.

### Crear una red de tipo bridge

```
docker network create <nombre red> -d bridge
```

### Crear un contenedor vinculado a una red

```
docker run -d --name <nombre contenedor> --network <nombre red> <nombre imagen>
```

### Para saber a qué red está conectado un contenedor

```
docker inspect <nombre contenedor>
```
ó
```
docker network inspect <nombre red> 
```

### Vincular contenedor a una red
```
docker network connect <nombre red> <nombre contenedor>
```

### Para desvincular un contenedor de una red
```
docker network disconnect <nombre red> <nombre contenedor>
```

### Para listar las redes existentes
```
docker network ls
```

### Crear los contenedores y las redes que se presentan en el esquema. Usar para todos los contenedores la imagen de nginx:alpine

![Imagen](esquema-ejercicio-redes.PNG)


**1. Crear las redes:**
```
docker network create net-curso01 -d bridge
docker network create net-curso02 -d bridge
```

**2. Crear los contenedores vinculados a sus respectivas redes:**
```
# Contenedores 1 y 2 conectados a net-curso01
docker run -d --name contenedor1 --network net-curso01 nginx:alpine
docker run -d --name contenedor2 --network net-curso01 nginx:alpine

# Contenedor 3 conectado inicialmente a net-curso01
docker run -d --name contenedor3 --network net-curso01 nginx:alpine

# Contenedor 4 conectado a net-curso02
docker run -d --name contenedor4 --network net-curso02 nginx:alpine
```

**3. Conectar contenedor3 también a la red net-curso02:**
```
docker network connect net-curso02 contenedor3
```
**Verificar las configuraciones:**
```
# Listar todas las redes
docker network ls

# Inspeccionar cada red para ver los contenedores conectados
docker network inspect net-curso01
docker network inspect net-curso02

```
<img width="1016" height="467" alt="image" src="https://github.com/user-attachments/assets/a5229d62-1316-4f7e-9aa6-df2951c8bb80" />

# COLOCAR UNA CAPTURA DE LAS REDES EXISTENTES CREADAS

Comando a ejecutar:
```bash
docker network ls
```
# COLOCAR UNA(S) CAPTURAS(S) DE LOS CONTENEDORES CREADOS EN DONDE SE EVIDENCIE A QUÉ RED ESTÁN VINCULADOS
**Inspeccionar cada red para ver los contenedores conectados:**
```
docker network inspect net-curso01
docker network inspect net-curso02
```
Para net-curso01:
<img width="939" height="888" alt="image" src="https://github.com/user-attachments/assets/f93526d3-6f1e-4621-829c-8f6edd991515" />
Para net-curso02:
<img width="992" height="824" alt="image" src="https://github.com/user-attachments/assets/7e452101-407d-455e-aa8d-f1a37ad2c135" />

<img width="588" height="182" alt="image" src="https://github.com/user-attachments/assets/d4c8f074-f6d9-47d9-a806-16436c405e50" />

### Para eliminar las redes creadas
```
docker network rm <nombre de la red>
```

