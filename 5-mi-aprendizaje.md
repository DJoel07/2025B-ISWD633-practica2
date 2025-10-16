# Mi Aprendizaje
# Dorian Joel Alban Lucas

Se he logrado los siguientes aprendizajes principales:

### 1. Variables de Entorno en Docker
**Antes:** Tenía conocimiento básico sobre la ejecución de contenedores Docker, pero no comprendía la importancia de las variables de entorno.

**Después:** Aprendí que:
- Las variables de entorno son fundamentales para configurar aplicaciones sin modificar el código
- Permiten separar la configuración del código, mejorando la portabilidad
- Son esenciales para aplicaciones como MySQL y WordPress que requieren credenciales y parámetros de conexión
- Se pueden definir usando la opción `-e` al crear contenedores
- Facilitan el despliegue en diferentes entornos (desarrollo, pruebas, producción)

**Beneficio profesional:** Esto me permite desarrollar aplicaciones más flexibles y seguras, siguiendo buenas prácticas de la industria.

### 2. Redes en Docker
**Antes:** Desconocía cómo los contenedores se comunican entre sí y con el exterior.

**Después:** Comprendí que:
- Docker maneja diferentes tipos de redes: bridge, host y none
- Las redes bridge personalizadas permiten aislar y organizar contenedores
- Los contenedores en la misma red pueden comunicarse usando sus nombres como hostnames
- Un contenedor puede estar conectado a múltiples redes simultáneamente
- El aislamiento de red mejora la seguridad de las aplicaciones

**Beneficio profesional:** Puedo diseñar arquitecturas de microservicios más seguras y organizadas, controlando el flujo de comunicación entre servicios.

### 3. Persistencia de Datos
**Antes:** Asumía que los datos en contenedores se mantenían automáticamente.

**Después:** Descubrí que:
- Los contenedores son efímeros: al eliminarlos, se pierden todos los datos almacenados en su sistema de archivos
- Los volúmenes de Docker son necesarios para persistir datos importantes
- La base de datos MySQL mantuvo los datos porque su contenedor no fue eliminado
- Es crucial identificar qué datos necesitan persistencia y usar volúmenes apropiadamente

**Beneficio profesional:** Puedo diseñar sistemas que no pierdan información crítica, garantizando la continuidad del negocio.

### 4. Mapeo de Puertos
**Antes:** No entendía completamente cómo acceder a servicios dentro de contenedores.

**Después:** Aprendí que:
- El mapeo de puertos conecta puertos del host con puertos internos del contenedor
- La opción `-p` permite especificar el mapeo (formato: `puerto_host:puerto_contenedor`)
- La opción `-P` mapea automáticamente todos los puertos expuestos
- Es posible tener múltiples contenedores usando el mismo puerto interno, siempre que usen diferentes puertos externos

**Beneficio profesional:** Puedo configurar aplicaciones web y APIs para que sean accesibles desde el exterior de manera controlada.

### 5. Integración de Servicios
**Antes:** Trabajaba con servicios de forma aislada.

**Después:** Implementé una aplicación completa (WordPress + MySQL) que requiere:
- Coordinación entre múltiples contenedores
- Configuración correcta de variables de entorno para la comunicación
- Uso de redes para conectar servicios
- Gestión de dependencias entre contenedores

**Beneficio profesional:** Estoy preparado para trabajar con arquitecturas de microservicios y aplicaciones distribuidas.

## Problemas Solucionados Durante la Práctica

### Problema 1: Contenedor MySQL no se ejecuta
**Descripción:** Al crear el contenedor MySQL sin variables de entorno, este se detenía inmediatamente.

**Solución:** 
```bash
docker run -d --name mysql-container -P -e MYSQL_ROOT_PASSWORD=pass123 mysql
```
Aprendí que MySQL requiere obligatoriamente al menos una variable de entorno de contraseña para inicializarse.

### Problema 2: Conflicto de nombres de contenedores
**Descripción:** Error al intentar crear un contenedor con un nombre ya existente.

**Solución:**
```bash
docker rm nombre-contenedor
```
Comprendí la importancia de gestionar el ciclo de vida de los contenedores y limpiar recursos no utilizados.

### Problema 3: Pérdida de datos de WordPress
**Descripción:** Al recrear el contenedor WordPress, se perdieron todos los cambios realizados.

**Causa:** Los datos se almacenaban solo en el sistema de archivos del contenedor, que es efímero.

**Solución:** Usar volúmenes para persistir datos:
```bash
docker run -d --name contenedor-wordpress -v wordpress_data:/var/www/html --network net-wp -p 9300:80 -e WORDPRESS_DB_HOST=contenedor-mysql wordpress
```

## Docker Secrets: Gestión de Datos Confidenciales

### ¿Qué son los Docker Secrets?
Docker Secrets es un mecanismo seguro para gestionar información confidencial como contraseñas, tokens, claves SSH y certificados en Docker Swarm.

### Características Principales:
1. **Cifrado:** Los secretos se cifran durante el tránsito y en reposo
2. **Acceso controlado:** Solo los servicios autorizados pueden acceder a secretos específicos
3. **Almacenamiento temporal:** Se montan en memoria (tmpfs) en los contenedores, nunca en disco
4. **Gestión centralizada:** Los secretos se gestionan a nivel de Swarm, no en contenedores individuales

### Creación de Secretos:
```bash
# Desde un archivo
echo "mi_password_segura" | docker secret create mysql_password -

# Desde stdin
docker secret create mysql_password /ruta/al/archivo
```

### Uso en Servicios:
```bash
docker service create \
  --name mysql \
  --secret mysql_password \
  -e MYSQL_ROOT_PASSWORD_FILE=/run/secrets/mysql_password \
  mysql:8
```

### Ventajas sobre Variables de Entorno:
- **Mayor seguridad:** No se exponen en `docker inspect` ni en logs
- **Rotación segura:** Se pueden actualizar sin reconstruir imágenes
- **Auditoría:** Control centralizado de acceso a información sensible
- **Cumplimiento normativo:** Ayuda a cumplir con regulaciones de seguridad

### Diferencias con Variables de Entorno:

| Aspecto | Variables de Entorno | Docker Secrets |
|---------|---------------------|----------------|
| Visibilidad | Visible en `docker inspect` | No visible en inspección |
| Almacenamiento | Texto plano | Cifrado |
| Disponibilidad | Docker standalone | Solo Docker Swarm |
| Gestión | Manual por contenedor | Centralizada |
| Seguridad | Baja | Alta |

### Limitaciones:
- **Solo disponible en Docker Swarm:** No funciona con `docker run` o `docker-compose` en modo standalone
- **Requiere orquestación:** Necesita tener un clúster Swarm inicializado
- **Complejidad adicional:** Mayor curva de aprendizaje comparado con variables de entorno

### Alternativas para Docker Compose (sin Swarm):
```yaml
version: '3.8'
services:
  mysql:
    image: mysql:8
    environment:
      MYSQL_ROOT_PASSWORD_FILE: /run/secrets/db_password
    secrets:
      - db_password

secrets:
  db_password:
    file: ./db_password.txt
```
