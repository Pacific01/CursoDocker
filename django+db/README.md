# Django+PostgreSQL

Aprender a levantar y coordinar un proyecto que requiere dos o mas dockers
mediante la prueba y error.

Durante este ejercicio vamos a levantar dos contenedores: una base de datos
Postgres y una aplicación django. En el proceso aprenderemos a gestionar mas de
un contenedor y sus respectivos volúmenes, así como a crear una networks de
docker para que los contenedores interactúen entre sí.

## Desarrollo en windows: Abriendo wsl en una powershell sin permisos de administración

## Empezaremos levantando la base de datos

Vamos a intentar levantar la última versión de postgres.

```sh
docker run postgres
```

Como podemos observar la terminal nos reporta el siguiente error:

```log
Error: Database is uninitialized and superuser password is not specified.
       You must specify POSTGRES_PASSWORD to a non-empty value for the
       superuser. For example, "-e POSTGRES_PASSWORD=password" on "docker run".

       You may also use "POSTGRES_HOST_AUTH_METHOD=trust" to allow all
       connections without a password. This is *not* recommended.

       See PostgreSQL documentation about "trust":
       https://www.postgresql.org/docs/current/auth-trust.html
```

El cual nos indica que el contenedor necesita ciertas variables de entorno y
como debemos pasárselas.

Intenta crear de nuevo el comando pero con este atributo añadido y ejecutar con
éxito la base de datos.

Una vez ejecutado el comando deberíamos ver la siguiente salida:

```sh
2022-02-16 15:58:35.253 UTC [1] LOG:  database system is ready to accept connections
```

Lo cual nos indica que todo ha ido correctamente.

Podemos detener la ejecución con Ctrl+C.

## Detalle extra nº1 - TAGs

Corred el comando:

```sh
docker images

# Si os fijáis apareceré la imagen de Postgres en la lista y su tag será 'latest'.
# Vamos a mirar que otros tags existen y que es esto del tag.
# (Postgres Docker Hub)[https://hub.docker.com/_/postgres]

docker run \
  -e POSTGRES_PASSWORD='secret' \
  postgres:14.1-alpine
```

## Primera parte del ejercicio terminada

⚠️ Avisar al profesor ⚠️

Como mejorar este comando. Podemos ir al hub a ver de que otras variables
dispone el contenedor, ponerle un nombre y ejecutarlo en modo detach.

```sh
docker run \
  -d \
  --name postgres \
  -e POSTGRES_PASSWORD='secret' \
  -e POSTGRES_DB='db' \
  -e POSTGRES_USER='postgres' \
  postgres:14.1-alpine
```

Comprueba que todo ha ido bien con el docker logs sobre el contenedor.

Ahora que has comprueba que todo va bien puedes pararlo por el momento.

Ya tenemos base de datos.

## Vamos a construir el BackEnd con Django

Para ello vamos a empezar clonando el proyecto entero [Pacific01/CursoDocker](https://github.com/Pacific01/CursoDocker)

```sh
git clone https://github.com/Pacific01/CursoDocker.git
```

Y entramos dentro del proyecto y dentro de la carpeta django+db

```sh
cd CursoDocker/django+db
```

Una vez dentro podemos observar que el proyecto consta de un Dockerfile y un
requirements.txt entre otros.

Vamos a construir la imagen a partir del Dockerfile.

```sh
docker build -t django .
```

Haced un docker images para ver que la nueva imágen está ahí.

```sh
docker images
```

Ahora que sabemos que contamos con nuestra imagen django podemos ejecutarla y
crear un cotenedor

```sh
docker run django
```

No ocurrirá nada, ya que el docker file no define un entrypoint
Vamos a entrar en el contenedor en el momento de crearlo

```sh
docker run -it django /bin/bash
```

Una vez dentro vamos a pedirle al django que nos cree un proyecto

```sh
django-admin startproject example .
```

Comprovamos que está ahí

```sh
ls
ls example
```

Salimos

```sh
exit
ls
ls example
```

El proyecto no está!!!!

Es debido a que no hemos enlazado el volumen del contenedor con el host.
Vamos a voler a intentarlo pero esta vez enlazaremos el volumen y además le
indicaremos a docker la id de grupo y usuario con la que queremos identificarnos
dentro del contenedor.

```sh
docker run -v `pwd`:/code --user "$(id -u):$(id -g)" -it django /bin/bash
```

Y creamos el proyecto

```sh
django-admin startproject example .
```

## Vamos a correr el proyecto

```sh
docker run \
  -v `pwd`:/code \
  django python manage.py runserver 0.0.0.0:8000

# No funcionaaaa!! => Faltan los puertos!
docker run \
  -v `pwd`:/code \
  -p 8000:8000 \
  django python manage.py runserver 0.0.0.0:8000
```

Ole ole y ole!! Ya tenemos nuestro proyecto django y nuestra base de datos...
Espera un momento pero no están juntas.

Vamos a juntarlas:
Empezaremos por conectar el django a la base de datos

```sh
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.postgresql',
        'NAME': 'db',
        'USER': 'postgres',
        'PASSWORD': 'secret',
        'HOST': 'db',
        'PORT': 5433,
    }
}
```

y por colocar los dockers en la misma red:

## Creamos la red

```sh
docker network create my-net
```

Postgres:

```sh
docker run \
  -d \
  --name db \
  -e POSTGRES_PASSWORD='secret' \
  -e POSTGRES_DB='db' \
  -e POSTGRES_USER='postgres' \
  -p 5433:5432 \
  --network my-net \
  postgres:14.1-alpine
```

Django

```sh
docker run \
  -v `pwd`:/code \
  -p 8000:8000 \
  --network my-net \
  django python manage.py runserver 0.0.0.0:8000
e``
