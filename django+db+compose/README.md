# Django+db+compose

Esto mas que un ejercicio es una demostración de que el ejercicio anterior se
puede administrar y mantener de una forma mucho mas sencilla gracias a [Docker
Compose](https://docs.docker.com/compose/)

(Opcional) Se podría poner como ejercicio la construcción el docker-compose.yml
a partir de los últimos dos comandos docker del ejercicio anterior (docker+db).

Creamos el proyecto:
```sh
docker-compose run web django-admin startproject example .
```

Cambiamos permisos:
```sh
sudo chown -R $USER:$USER .
```

Editamos settings.py
```sh
# settings.py

DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.postgresql',
        'NAME': 'db',
        'USER': 'postgres',
        'PASSWORD': 'secret',
        'HOST': 'db',
        'PORT': 5432,
    }
}
```

Levantamos todo:
```sh
docker-compose up
```
