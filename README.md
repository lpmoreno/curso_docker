# Author: Laura Paneque

# 1. Creando imágenes

## Paso 1

Ejecutamos un contenedor basado en la imagen: ubuntu y accedemos a la terminal del contenedor.

```
docker run --name ejercici1_ct -it ubuntu /bin/bash
```

![Imagen 1. Creación del contenedor ](capturas/Captura1.png)

Al utilizar la opción it, automáticamente accedemos a la terminal del contenedor. Actualizamos e instalamos curl:

```bash
apt-get update
apt-get install curl
```

Comprueba que funciona:

```bash
curl --version
```

![Imagen 2. Comprobar que curl se ha instalado ](capturas/Captura2.png)

Para salir del contenedor escribimos exit.

---

## Pregunta

¿Con qué comando podrías **guardar los cambios del contenedor como una
nueva imagen**?

Para guardar los cambios (la instalación de curl) en una nueva imagen Docker, debemos usar el comando 

```
docker commit ejercicio1_ct ubuntu_con_curl

docker images
```
Si consultamos las imágenes, veremos la nueva imagen creada a partir del contenedor con curl.

![Imagen 3. CGuardar cambios en imagen ](capturas/Captura3.png)

---

## Paso 2 --- Dockerfile

Creamos un archivo `Dockerfile` que haga lo mismo automáticamente.


```dockerfile
FROM ubuntu

RUN apt-get update && apt-get install -y curl
```


Ahora construimos la imagen a partir del archivo anterior y creamos un contenedor.

```
docker build --tag ubuntu-curl-v2 .
docker images
```

![Imagen 4. Crear imagen desde Dockerfile ](capturas/Captura4.png)

Comprobmos que `curl` está instalado. Para ello creamos un contenedor nuevo a partir de la imagen anterior:

```
docker run --name ejercicio1_ct2 -it ubuntu_curl_v2 /bin/bash
```

![Imagen 5. Crear contenedor desde nueva imagen ](capturas/Captura5.png)
---

## Pregunta

¿Qué comando permite ver las **capas de una imagen Docker**?
Con el siguiente comando:
```
docker image history ubuntu_curl_v2
``` 

---

# 3. Volúmenes persistentes

Ejecutamos un contenedor de postgres. Vamos acrear un volumen persistente montado en /var/lib/postgresql/data

```
docker run --name my_postgres -e POSTGRES_PASSWORD=1234 -d -v pgdata:/var/lib/postgresql -p 5432:5432 postgres
```

Como la versión de postgres descargada es superior a la 18, la ruta del volumen ha cambiado de /var/lib/postgres/data a /var/lib/postgres. Si no se tiene esto en cuenta, el contenedor dará error al iniciar y se detendrá.

Una vez iniciado el contenedor, accededos a la terminal con el comando siguiente:

```
docker exec -it my_postgres /bin/bash
```

Una vez dentro del contenedor cambiamos de usuario a postgres para acceder a la base de datos con el cliente psql:

```
su - postgres
psql
```

![Imagen 6. Creación contenedor con volumen mount ](capturas/Captura6.png)
---

## Crear tabla

Creamos la tabla e insertamos un registro:

```sql
CREATE TABLE items (
 id SERIAL PRIMARY KEY,
 name TEXT
);
```


```sql
INSERT INTO items(name) VALUES ('item1');
```

---

## Comprobación

Para comprobar, seguimos los siguientes pasos:

1.  Paro el contenedor
2.  Elimino el contenedor
3.  Creo un nuevo contenedor usando **el mismo volumen**
4.  Compruebo que los datos siguen existiendo.

![Imagen 7. Comprobación de datos en volumen ](capturas/Captura7.png)

---

# 4. Bind mounts

Creo un archivo en mi máquina:

    index.html


```html
<h1>Hola Docker</h1>
```

---

Ejecuto un contenedor  de `nginx`:

- mapeo el puerto `80`
- monto el archivo en: /usr/share/nginx/html/index.html

```
docker run -d --name laura-nginx -p 8080:80 --mount type=bind,source="$(pwd)"/web-content,target=/usr/share/nginx/html nginx
```

Abro el navegador y podemos observar cómo nginx carga nuestro index.html

![Imagen 8. Comprobación de datos en volumen ](capturas/Captura8.png)

---

Pregunta:

¿Qué ocurre si modificas el archivo `index.html` en tu máquina?
Al modificar el archivo, nginx automáticamente detecta el cambio realizado tal y como muestran las siguientes imágenes

![Imagen 9. Comprobación de datos en volumen ](capturas/Captura9.png)
![Imagen 10. Comprobación de datos en volumen ](capturas/Captura10.png)

---


