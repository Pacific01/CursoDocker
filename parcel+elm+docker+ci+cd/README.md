# Parcel + Elm + Docker + CI/CD

# Requisitos:
- Cuenta de [GitHub](https://github.com/)
- Cuenta de [Buddy](https://app.buddy.works/)
- Cuenta de [Netlify](https://www.netlify.com/)

# Tareas:

## Primera parte: Comprobar que todo funciona

- Empezaremos Haciendo un Fork del proyecto base en GitHub [Parcel + Elm +
  Docker + CI/CD](https://github.com/Pacific01/parcel-elm-docker-ci-cd)

- Lo clonamos en local y lo intentamos ejecutar tal cual indica el readme del
  proyecto.

## Segunda parte: Levantar la infra estructura

- Entramos en [Buddy](https://app.buddy.works/) y lo conectamos con GitHub:
  + Accediendo a Buddy > Projects > + Create new Project > Get Provider > +
  + Aceptamos la conexión.
  + Una vez aceptada la conexión entre GitHub y Buddy añadimos el proyecto
    haciendo click en su nombre.
  + Saldrá un menú para crear una Pipeline:
    - Le ponemos un nombre
    - Seleccionamos un trigger: On push
    - Seleccionamos una branch: Main

## Tercera parte: Definir la pipeline

Ahora vamos a definir el proceso que nos levantará las imágenes del proyecto y
nos hará el build.

- Abrimos el proyecto y vamos a actions
- Le damos al + y buscamos "Build Image" y la seleccionamos
- Guardamos esta acción

- (Opcional) Podemos darle a "Run pipeline", arriba a la derecha, para comprobar
  que por ahora todo ha ido bien y para ver como funcionan las runs. Al darle
  deberemos indicarle un nombre y posteriormente ejecutará nuestra Pipeline, que
  por ahora solo consta del build de la imagen del proyecto parcel.

- Le volvemos a dar al + que hay debajo de nuestra primera acción y buscamos una
  segunda acción "Run Image" y la seleccionamos.
- En la primera pestaña "Environment" nos aseguramos de que las siguientes
  opciones estén seleccionadas:
  + Docker image -> "User docker image in previous action"
  + Action that build the docker image -> "Image Build in previous action"
- En la segunda pestaña "CMD" rellenamos el input "Entrypoint" con el siguiente
  comando: `npm install`
- En la tercera pestaña "Cache":
  + Attach pipeline filesystem ->  "Mount filesystem to the Docker container in
    which this action is run"
  + Mount container path -> "/code"

- Ya podemos guardar esta action en nuestra pipeline.

- (Opcional) Podemos volver a darle a "Run pipeline", arriba a la derecha, para
  comprobar que por ahora todo ha ido bien y para ver como funcionan las runs.

- Creamos una tercera action, del mismo tipo que la anterior y con las mismas
  opciones, con la diferencia de que en el campo Entrypoint hemos de poner el
  siguiente comando: `npm run build`

- Ahora ya podemos ejecutar la pipeline con el botón "Run pipeline" arriba a la
  derecha, la cual debería hacer build de nuestro proyecto.

## Cuarta Parte: Publicar la web en Netlify

- Volvemos al proyecto (Normalmente arriba de todo hay un botón que nos lleva a
  la base del proyecto) También podemos ir mediante el menú de la izquierda.
- Esta vez abriremos la pestaña "Filesystem"
- Si todo ha ido bien, debería haber una carpeta llamada "dist" la cual podemos
  descargarnos mediante: "los tres puntitos" -> download. Esto descargará un
  .zip

- Vamos a la app de [Netlify](https://app.netlify.com/) deberemos loguearnos si
  no estamos logueados ya.
- Abrimos los sites.
- Debajo de la lista de sites aparece un cartel que pone lo siguiente:
  + "Want to deploy a new site without connecting to Git?"
  + "Drag and drop your site output folder here"
- Arrastramos la descarga del dist y lo colocamos ahí: En unos instantes netlify
  creará una web y hará un deploy de nuestro código. Podemos comprobar que todo
  ha ido bien abriendo el link que nos aparecerá, mas o menos, arriba a la
  derecha.

Ya tenemos nuestra web!! Pero estaría guay que el proceso de deploy fuera
automático.

## Quinta parte: Deploy automático.

- Volvemos a Buddy y añadimos una nueva acción al final de la pipeline del
  proyecto.
- Buscamos Netlify, al seleccionar lo primero que deberemos hacer es
  conectarlos.
- Una vez conectados debemos editar el comando por defecto añadiéndole `/dist`
  de modo que publique los archivos correctos.
  + Comando original: `netlify deploy --dir=. --prodIfUnlocked`
  + Comando final: `netlify deploy --dir=./dist --prodIfUnlocked`
- Seleccionamos la integración adecuada: La cuenta que acabamos de conectar.
- Y seleccionamos la web que se ha creado en la parte anterior en el Site.
  Seguramente será una string aleatoria parecida a esta:
  stupefied-jennings-6cc54f

- Guardamos la action.

- (Opcional) Podemos darle a "Run pipeline" para comprobar que todo ha ido bien,
  pero esta vez no veremos cambios en la web, ya que el deploy actual será el
  mismo que el anterior.


## Sexto paso: Comprobación final.

Vamos a editar algo del proyecto original que hemos clonado y levantado en
local:
- Editaremos la línea 7 del archivo `src/Main.elm` cambiando la string "Hello,
  Docker Class!" por cualquier otra string.
- Git add, git commit y git push.

Podremos comprobar que en cuestión de segundos Buddy ha detectado un cambio en
el proyecto y ha lanzado la pipeline, si todo esta bien cuando esta termine, en
la web creada por netlify podremos ver el cambio.

---

Disclaimer 1: Esta infraestructura está sobre dimensionada debido a fines
académicos.

Disclaimer 2: Pese a estar sobre dimensionada, existen otras partes que no hemos
añadido debido al limitado tiempo que tenemos en la clase para hacer el
ejercicio. Estas partes serían por ejemplo: Testing, Buckups y Avisos por
diversos canales del estado de la Pipeline.
