# Tarea 3: Marco Aguilar - Rocío Contreras Águila

Haz un fork de este repo

NOTA: Esta tarea se puede ejecutar en el playground de docker.

https://labs.play-with-docker.com/

Actividad 1: Mi primer docker-compose
Revisa el archivo docker-compose.yml.

Revisa el archivo .env-sample

El archivo .env-sample define las variables de entorno usadas en el archivo docker-compose.yml.

Copia el archivo .env-sample a .env, en linux esto se hace así:

    cp .env-sample .env
Edita el archivo .env. Cambia los valores de las variables que quieras. Graba el archiv .env. (En linux puedes usar el editor nano o vim).

Ejecuta docker-compose de este modo:

    docker-compose --env-file .env-sample up -d
Ahora revisa cuantos contenedores están corriendo de este modo:

    docker ps
Ingresa a la base de datos con este comando (reemplaza los valores que corresponde a tu configuración en el archivo .env):

    docker-compose exec -it postgres psql -U POSTGRES_USER POSTGRES_DB
Por ejemplo, usando los valores que están en el archiv .env-sample, debes hacer:

    docker-compose exec -it postgres psql -U postgres usuarios
Con esto puedes ejecutar comandos en psql para revisar la base de datos:

    psql (15.4)
    Type "help" for help.

    usuarios=# select * from users;
Para salir escribe \q.

Deten tus contenedores con este comando:

    docker-compose down
Actividad 2: la aplicación chatroom
Edita el archivo docker-compose.yml y agrega los servicios de frontend y backend:

  frontend:
    build: chat-frontend
    restart: always
    environment:
      - FRONT_PORT
    expose: 
      - 80
    ports:
      - ${FRONT_PORT}:80
    volumes:
      - ./nginx-conf/default.conf:/etc/nginx/conf.d/default.conf
 
  users-svc:
    build: users-svc
    command: "node app.js" 
    restart: always
    expose:
      - 3000
    environment:
      - JWT_SECRET
      - POSTGRES_USER
      - POSTGRES_PASSWORD
      - POSTGRES_DB
      - POSTGRES_PORT=5432
      - POSTGRES_SERVER=postgres
      - PORT=3000
      - CONNECTION_URL=postgresql://${POSTGRES_USER}:${POSTGRES_PASSWORD}@postgres:5432/${POSTGRES_DB}
    depends_on:
      - postgres
  
  ws-server:
    build: ws-server
    command: "node index.js"
    restart: always
    expose:
      - 3001
    environment:
      - PORT=3001
Asegurate de colocar un valor adecuado a la variable FRONT_PORT`` en tu archivo .env`

Levanta los contenedores con el comando:

    docker-compose up -d
Navega con browser a la dirección localhost:FRONT_PORT, deberias ver la pagina de login.

Registra un usuario, la aplicación debería estar corriendo correctamente.

Preguntas

# Pregunta 1
Revisa el archivo Dockerfile en la carpeta users-svc y compáralo con el mismo archivo en la carpeta ws-server. ¿Qué te llama la atención? Ahora revisa la sentencia command para los respectivos servicios en el archivo docker-compose.yml. ¿Qué concluyes?

RESPUESTA: el archivo Dockerfile de las carpetas users-svc y ws-server son idénticos. al revisar la sentencia command se puede observar que una de ellas ejecuta el servicio app.js y la otra al servicio index.js

# Pregunta 2
Revisa el archivo Dockerfile en la carpeta frontend. ¿Qué te llama la atención? ¿En qué es diferente de los otros archivos Dockerfile?
RESPUESTA: crea un build directory con un production build de la app. Dentro del directorio build/static estarán los archivos javascript y CSS

# Pregunta 3
¿Para qué sirve el servicio flyway? ¿Qué pasa al hacer docker ps con respecto a este servicio?
RESPUESTA: Es una herramienta de código abierto que sirve para procesos de migración de bases de datos. Al ejecutar docker ps el servicio no aparece, porque se encuentra asociado a servicio de postgres.

# Pregunta 4
¿Cuantas imágenes se crean? ¿Cuántos contenedores están activos?
RESPUESTA: se crean 4 imágenes y 4 contenedores activos

# Pregunta 5
Deten los contenedores con docker-compose down, luego reinicia con docker-compose up -d. Ingresa a la base de datos. ¿Qué pasa con los datos?

RESPUESTA: Los datos no se encuentran disponibles

Baja los contenedres. Crea un volumen para postgres agregando estas sentencias en el servicio postgres:
 volumes:
   - ./data:/var/lib/postgresql/data
Reinicia los contenedores. Explica qué pasa con la base de datos después de hacer esto.

RESPUESTA: la base de datos no contiene datos

¿Qué pasa con la carpeta data, qué crees que contiene?

RESPUESTA: la carpeta data se crea despues de iniciar los contenedores producto de la configuración del volumen en el archivo de servicios. Esta carpeta contiene directorios y archivos de configuración relacionados con replicas de la base de datos.
