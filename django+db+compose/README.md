# Django+PostgreSQL

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
