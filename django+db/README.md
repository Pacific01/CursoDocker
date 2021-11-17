# Django+PostgreSQL

Aprender a levantar y coordinar un proyecto que requiere dos o mas dockers
mediante la prueba y error.

Empezaremos levantando la base de datos.

# Run database image
```sh
docker run postgres

# Añade la contraseña a la base de datos
docker run \
  -e POSTGRES_PASSWORD='secret' \
  -e POSTGRES_DB='db' \
  -e POSTGRES_USER='postgres' \
  postgres
```

# Detalle extra nº1 - TAGs

Corred el comando:
```sh
docker images

# Si os fijáis apareceré la imagen de Postgres en la lista y su tag será 'latest'.
# Vamos a mirar que otros tags existen y que es esto del tag.
# (Postgres Docker Hub)[https://hub.docker.com/_/postgres]

docker run \
  -e POSTGRES_PASSWORD='secret' \
  -e POSTGRES_DB='db' \
  -e POSTGRES_USER='postgres' \
  postgres:14.1-alpine
```

Ahora solo faltaría ponerle un nombre y hacerle un detach.

```sh
docker run \
  -d \
  --name postgres \
  -e POSTGRES_PASSWORD='secret' \
  -e POSTGRES_DB='db' \
  -e POSTGRES_USER='postgres' \
  postgres:14.1-alpine
```

Ya tenemos base de datos.

# Build the django image
```sh
docker build -t django .

# Haced un docker images para ver que la nueva imágen está ahí.

docker run django

# No ocurrirá nada, ya que el docker file no define un entrypoint
# Vamos a entrar en el contenedor en el momento de crearlo
docker run -it django /bin/bash

# y vamos a pedirle al django que nos cree un proyecto
django-admin startproject example .

# Comprovamos que está ahí
ls
ls example

# Salimos
exit
ls
ls example

# El proyecto no está!!!!

# Es devido a que no hemos enlazado el volumen del contenedor con el host.
# Vamos a voler a intentarlo pero esta vez enlazaremos el volumen

docker run -v `pwd`:/code -it django /bin/bash

# Y creamos el proyecto
django-admin startproject example .
```

# Vamos a correr el proyecto

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

# Creamos la red
```sh
docker network create my-net
Postgres:
```sh
docker run \
  -d \
  --name postgres \
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
```


# Guardar las envs en un file (opcional)
```sh
docker run --env-file ./env
```
