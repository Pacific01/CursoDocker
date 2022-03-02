# Introducción a Docker

Este curso consta de unas diapositivas iniciales en las cuales se explica que es
docker, que pretende resolver y como funciona,... Junto con una serie de
directorios, en cada uno de los cuales se encuentra un readme con un ejercicio
guiado.

Ha sido impartido en [Cibernarium de Barcelona Activa](https://cibernarium.barcelonactiva.cat/home)

Lo primero que deberemos hacer es instalar docker y hacer el tutorial, el cual
nos enseñará los comandos básicos de docker. Posteriormente haremos el ejercicio
django+db, el cual es una copia de uno de los ejercicios oficiales de
[DockerCompose](https://docs.docker.com/samples/django/), pero con una serie de
cambios y centrado en aprender a crear comandos de docker bastante complejos.
Después podemos seguir con los ejercicios.

Disclaimer: Todos los comandos han sido ejecutados en una terminal de linux.

# Agenda y ejercicios

* Instalar docker:
  * [Instalar WSL](https://docs.microsoft.com/en-us/windows/wsl/install)
  * [Instalar actualización de Kernel de Linux](https://docs.microsoft.com/es-es/windows/wsl/install-manual#step-4---download-the-linux-kernel-update-package)
  * [Instalar Docker Desktop en Windows](https://docs.docker.com/desktop/windows/install/)

* Correr el hello world en docker `docker run hello-world`
* Completar el tutorial inicial de Docker

* Ejecutar algunos ejemplos:
  * Nginx: `docker run --name -v ./:/usr/share/nginx/html:ro -d nginx`

* Aprender los primeros comandos:
  * `docker ps`
  * `docker run`
  * `docker rm`
  * `docker logs`
  * `docker exec`

- Django+db
- Instalar Docker-compose
- Django+db+compose
- parcel+elm+docker+ci+cd
