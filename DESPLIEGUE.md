# Despliegue automatizado de la imagen Docker del programa:

Primero, realizamos un fork del repositorio principal:

https://github.com/revilofe/2526_DAW_u2_springboot

Esta aplicación como indica la documentación principal se trata de un CRUD con persistencia para registrar usuarios.

Realizamos un *fork* de ella y clonamos nuestro repositorio.

## Dockerfile y Docker-compose:

El proyecto ya incluye un ``Dockerfile`` y un ``docker-compose`` que construyen la imagen de forma automatizada, para esta práctica, su análisis no es importante, pero levantan todo el contenido de la aplicación web.

## GitHub Actions:

Para automatizar todo completo y tener el contenedor siempre disponible y en su última versión, se crea un *Action* de GitHub para automatizar esto. El archivo se encuentra en ``.github/workflows/docker-image.yml`` para que GitHub Actions lo encuentre.

La acción lo que realiza es:

1. **Se activa automáticamente** cuando hacemos un *push* o una *pull request* a la rama `master`. Esto significa que cada vez que subamos cambios al repositorio, la acción se ejecutará sola.

2. **Descarga el código fuente** del repositorio para poder trabajar con él en el servidor de GitHub.

3. **Inicia sesión en Docker Hub** usando las credenciales que hemos guardado como secretos en GitHub (`DOCKER_USERNAME` y `DOCKER_PASSWORD`). Esto es necesario para poder subir la imagen al registro de Docker Hub.

4. **Genera las etiquetas (tags)** para la imagen Docker. Se crean dos etiquetas:
   - `latest`: indica que es la versión más reciente
   - Un identificador único basado en el SHA del commit

5. **Construye la imagen Docker** usando el `Dockerfile` del proyecto y la **sube a Docker Hub**. Solo se sube si es un *push* directo, no si es una *pull request* (por seguridad).

6. **Genera una certificación de procedencia** (*attestation*) que verifica que la imagen fue construida en GitHub Actions. Esto añade una capa de seguridad y confianza sobre el origen de la imagen.

### Resultado final

Una vez ejecutada la acción correctamente, la imagen estará disponible en Docker Hub en:

```
sdurutr436/aplicacion-springboot-sergio-duran-utrera-2daw-despliegue
```

Cualquier persona podrá descargarla y ejecutarla con:

```bash
docker pull sdurutr436/aplicacion-springboot-sergio-duran-utrera-2daw-despliegue
docker run -p 8080:8080 sdurutr436/aplicacion-springboot-sergio-duran-utrera-2daw-despliegue
```

### Configuración necesaria en GitHub

Para que la acción funcione, es necesario configurar dos **secretos** en el repositorio de GitHub (Settings → Secrets and variables → Actions):

| Secreto | Descripción |
|---------|-------------|
| `DOCKER_USERNAME` | Tu nombre de usuario de Docker Hub |
| `DOCKER_PASSWORD` | Un Access Token generado en Docker Hub (no la contraseña) | 