### Crear contenedor de Postgres sin que exponga los puertos. Usar la imagen: postgres:15-alpine3.21
```
docker run -d --name postgres-server -e POSTGRES_PASSWORD=postgres123 postgres:15-alpine3.21
```
**Nota:** Al no usar `-p` o `-P`, el contenedor no expone puertos al host, solo usa su puerto interno 5432.

### Crear un cliente de postgres. Usar la imagen: dpage/pgadmin4
```
docker run -d --name pgadmin-client -p 8080:80 -e PGADMIN_DEFAULT_EMAIL=admin@admin.com -e PGADMIN_DEFAULT_PASSWORD=admin123 dpage/pgadmin4
```
<img width="1869" height="898" alt="image" src="https://github.com/user-attachments/assets/9cba8f1d-11c7-4736-83bf-99fdecf6f9b2" />

**Verificar que ambos contenedores estén corriendo:**
```
docker ps
```
<img width="1884" height="276" alt="image" src="https://github.com/user-attachments/assets/ba65eed5-ff5d-4a7a-a252-9db77d48f513" />

**Obtener la IP del servidor postgres:**
```
docker inspect -f '{{range.NetworkSettings.Networks}}{{.IPAddress}}{{end}}' postgres-server
```
<img width="1308" height="65" alt="image" src="https://github.com/user-attachments/assets/fdc9d457-402a-4781-9e78-35a96bd2cec3" />

La figura presenta el esquema creado en donde los puertos son:
- a: **8080** (puerto del host mapeado a pgAdmin)
- b: **80** (puerto interno del contenedor pgAdmin)
- c: **5432** (puerto interno del contenedor PostgreSQL, no expuesto al host)

![Imagen](esquema-2-ejercicio.PNG)

## Desde el cliente
### Acceder desde el cliente al servidor postgres creado.
# COMPLETAR CON UNA CAPTURA DEL LOGIN
Abrir el navegador y acceder a: `http://localhost:8080`
Se inicio sesión con las siguientes credenciales:
   - Email: `admin@admin.com`
   - Password: `admin123`
<img width="1651" height="872" alt="image" src="https://github.com/user-attachments/assets/c36582c4-9fa9-42ed-9ee3-e44ca0f3227a" />
<img width="875" height="696" alt="image" src="https://github.com/user-attachments/assets/eaa25fbf-0fb6-42e5-a90e-8d06d6308eed" />
<img width="1919" height="657" alt="image" src="https://github.com/user-attachments/assets/4a4a160d-5732-4dbc-bc47-02a49976b175" />

### Crear la base de datos info, y dentro de esa base la tabla personas, con id (serial) y nombre (varchar), agregar un par de registros en la tabla, obligatorio incluir su nombre.
<img width="1919" height="772" alt="image" src="https://github.com/user-attachments/assets/08d19f41-af36-4292-ac37-1b9bc708ea01" />

## Desde el servidor postgresl
### Acceder al servidor
```bash
docker exec -it postgres-server psql -U postgres
```
### Conectarse a la base de datos info
```bash
\c info
```

### Realizar un select *from personas
# AGREGAR UNA CAPTURA DE PANTALLA DEL RESULTADO
<img width="770" height="353" alt="image" src="https://github.com/user-attachments/assets/681ec694-a23d-4624-8375-fc521143fb31" />

