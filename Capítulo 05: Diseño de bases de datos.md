Creación de la base de datos
----------------------------

Recordemos del tercer capítulo cómo crear la base de datos para nuestras películas:

~~~~ {.sql}
DROP DATABASE IF EXISTS videoteca;
CREATE DATABASE videoteca;
~~~~

En esta base de datos tendremos una tabla:

~~~~ {.sql}
USE peliculas;

CREATE TABLE pelicula(
  titulo VARCHAR(64),
  director VARCHAR(128),
  actor VARCHAR(128)
);
~~~~

Realicemos algunas inserciones:

~~~~ {.sql}
USE videoteca;

INSERT INTO pelicula(titulo,director,actor)
VALUES('Blade Runner', 'Ridley Scott', 'Harrison Ford');

INSERT INTO pelicula(titulo,director,actor)
VALUES('Alien', 'Ridley Scott', 'Sigourney Weaver');

INSERT INTO pelicula(titulo,director,actor)
VALUES('Doce monos', 'Terry Gilliam', 'Bruce Willis');

INSERT INTO pelicula(titulo,director,actor)
VALUES('Contact', 'Robert Zemeckis', 'Jodie Foster');

INSERT INTO pelicula(titulo,director,actor)
VALUES('Tron', 'Steven Lisberger', 'Jeff Bridges');
~~~~

El valor NULL
-------------

Cualquier columna que creemos dentro de una tabla, independientemente de su tipo, puede almacenar este valor, a no ser que explícitamente se diga lo contrario. Fíjese en la siguiente consulta:

~~~~ {.sql}
USE videoteca;

SELECT
  titulo,
  CONCAT(director, ', ', actor)
FROM
  pelicula;
~~~~

Hagamos ahora una nueva inserción:

~~~~ {.sql}
USE videoteca;

INSERT INTO pelicula(titulo, director)
VALUES('Naves misteriosas', 'Douglas Trumbull');
~~~~

Como puede comprobar, no hemos indicado ningún actor. Si vuelve a ejecutar la consulta de selección que vimos al principio verá que la concatenación de director y actor vale NULL. Para evitar la inserción de valores nulos en los campos de la tabla de películas podemos cambiar la forma en la que creamos la tabla, usando ésta en su lugar:

~~~~ {.sql}
USE videoteca;

DROP TABLE IF EXISTS pelicula;
CREATE TABLE pelicula(
  titulo VARCHAR(64) NOT NULL,
  director VARCHAR(128) NOT NULL,
  actor VARCHAR(128) NOT NULL
);
~~~~

Un paso más consiste en utilizar la instrucción ALTER TABLE, que vimos en el capítulo 3, de la siguiente forma:

~~~~ {.sql}
USE videoteca;

ALTER TABLE pelicula
  MODIFY titulo VARCHAR(64) NOT NULL,
  MODIFY director VARCHAR(128) NOT NULL
    DEFAULT '',
  MODIFY actor VARCHAR(128) NOT NULL
    DEFAULT '';
~~~~

Si tras modificar la tabla ejecutamos la consulta de selección que concatena el nombre del director y el del actor, veremos que el resultado ya no incluye ningún NULL.

Claves primarias
----------------

Para explicar qué es una clave primaria, volvamos a la tabla <u>pelicula</u>. Vamos a insertar en ella otra película más, esta vez la segunda parte de Alien, llamada Aliens, dirigida por James Cameron y protagonizada de nuevo por Sigourney Weaver:

~~~~ {.sql}
USE videoteca;

INSERT INTO pelicula
VALUES(
  'Alien',
  'James Cameron',
  'Sigourney Weaver'
);
~~~~

Pero nos hemos equivocado, el título es incorrecto. Aunque esto no ha impedido la inserción de dos películas con el mismo título. Cambiemos la sentencia para crear la tabla de manera que el título sea la clave primaria:

~~~~ {.sql}
USE videoteca;

DROP TABLE IF EXISTS pelicula;
CREATE TABLE pelicula(
  titulo VARCHAR(64) NOT NULL,
  director VARCHAR(128) NOT NULL DEFAULT '',
  actor VARCHAR(128) NOT NULL DEFAULT '',
  PRIMARY KEY(titulo)
);
~~~~

Si no quiere tener que volver a insertar de nuevo los datos de todas las películas puede modificar la tabla así:

~~~~ {.sql}
USE videoteca;
ALTER TABLE pelicula ADD PRIMARY KEY(titulo);
~~~~

Como verá, se produce un error: existe un valor duplicado para la clave que estamos intentado crear: Alien aparece dos veces. Solucionarlo es bien sencillo:

~~~~ {.sql}
USE videoteca;
UPDATE
  pelicula
SET
  titulo = 'Aliens'
WHERE
  director = 'James Cameron';
~~~~

Columnas de incremento automático
---------------------------------

Para crear una tabla con una columna de incremento automático que, además, sea la clave primaria, podemos utilizar el siguiente script:

~~~~ {.sql}
USE videoteca;

CREATE TABLE pelicula(
  id INT UNSIGNED NOT NULL AUTO_INCREMENT,
  titulo VARCHAR(64) NOT NULL,
  director VARCHAR(128) NOT NULL
    DEFAULT '',
  actor VARCHAR(128) NOT NULL
    DEFAULT '',
  PRIMARY KEY(id)
);
~~~~

También podemos lograr el mismo resultado sin perder los registros existentes en la base datos así:

~~~~ {.sql}
USE videoteca;
ALTER TABLE pelicula
  DROP PRIMARY KEY,
  ADD id INT NOT NULL AUTO_INCREMENT FIRST,
  ADD PRIMARY KEY(id);
~~~~

El modelo relacional
--------------------

Además de la tabla de películas, vamos a crear otra para llevar un registro de las que prestamos. Si queremos relacionarlas es necesario que ambas sean de tipo INNODB:

~~~~ {.sql}
USE videoteca;

DROP TABLE IF EXISTS pelicula;
CREATE TABLE pelicula(
  id INT UNSIGNED NOT NULL AUTO_INCREMENT,
  titulo VARCHAR(64) NOT NULL,
  director VARCHAR(128) NOT NULL
    DEFAULT '',
  actor VARCHAR(128) NOT NULL
    DEFAULT '',
  PRIMARY KEY(id)
)TYPE INNODB;

DROP TABLE IF EXISTS prestamo;
CREATE TABLE prestamo (
  id INT UNSIGNED NOT NULL AUTO_INCREMENT,
  idpelicula INT UNSIGNED NOT NULL,
  persona VARCHAR(128) NOT NULL,
  fecha DATE NOT NULL,
  PRIMARY KEY(id),
  FOREIGN KEY(idpelicula)
    REFERENCES pelicula(id)
)TYPE INNODB;
~~~~

Si no queremos perder los datos de la tabla de películas podemos cambiar su tipo así:

~~~~ {.sql}
USE videoteca;
ALTER TABLE pelicula TYPE=InnoDB;
~~~~

Añadamos un índice a la tabla de préstamos para poder establecer la relación con la tabla de películas:

~~~~ {.sql}
USE videoteca;

DROP TABLE prestamo;
CREATE TABLE prestamo (
  id INT UNSIGNED NOT NULL AUTO_INCREMENT,
  idpelicula INT UNSIGNED NOT NULL,
  persona VARCHAR(128) NOT NULL,
  fecha DATE NOT NULL,
  INDEX prestamo_FK(idpelicula),
  PRIMARY KEY(id),
  FOREIGN KEY(idpelicula)
    REFERENCES pelicula(id)
)TYPE=InnoDB;
~~~~

Vamos a prestar Contact a Kake. Si MySQL asignó automáticamente a esa película el identificador 4:

~~~~ {.sql}
USE videoteca;
INSERT INTO prestamo(idpelicula, persona, fecha)
VALUES(4, 'Kake', CURRENT_DATE());
~~~~

El siguiente intento de registrar un préstamo fallará, puesto que no existe ninguna película con el identificador proporcionado:

~~~~ {.sql}
USE videoteca;
INSERT INTO prestamo(idpelicula, persona, fecha)
VALUES(123, 'Germán', CURRENT_DATE());
~~~~

División de tablas
------------------

Vamos a añadirle algunos campos más a la tabla de películas, para guardar otros datos que pueden resultarnos interesantes:

~~~~ {.sql}
USE videoteca;

DROP TABLE IF EXISTS pelicula;
CREATE TABLE pelicula (
  id INTEGER UNSIGNED NOT NULL AUTO_INCREMENT,
  titulo VARCHAR(64) NOT NULL,
  director VARCHAR(128) NOT NULL DEFAULT '',
  actor VARCHAR(128) NOT NULL DEFAULT '',
  soporte VARCHAR(3) NOT NULL DEFAULT '',
  imdb_director VARCHAR(32) NOT NULL DEFAULT '',
  imdb_actor VARCHAR(32) NOT NULL DEFAULT '',
  genero VARCHAR(2) NOT NULL DEFAULT '',
  genero_desc VARCHAR(32) NOT NULL DEFAULT '',
  PRIMARY KEY(id)
)
TYPE=InnoDB;
~~~~

Insertemos algunos registros:

~~~~ {.sql}
USE videoteca;
INSERT INTO pelicula(
  titulo, director, actor,
  soporte, imdb_director, imdb_actor,
  genero, genero_desc
)
VALUES(
  'Blade Runner', 'Ridley Scott', 'Harrison Ford',
  'DVD', 'nm0000631', 'nm0000148',
  'CF', 'Ciencia-ficción'
);

INSERT INTO pelicula(
  titulo, director, actor,
  soporte, imdb_director, imdb_actor,
  genero, genero_desc
)
VALUES(
  'Gladiator', 'Ridley Scott', 'Russell Crowe',
  'DVD', 'nm0000631', 'nm0000128',
  'A', 'Aventuras'
);

INSERT INTO pelicula(
  titulo, director, actor,
  soporte, imdb_director, imdb_actor,
  genero, genero_desc
)
VALUES(
  'A propósito de Henry', 'Mike Nichols',
  'Harrison Ford', 'VHS', 'nm0001566', 'nm0000148',
  'D', 'Drama'
);
~~~~

Vamos a sacar los datos de los directores y los actores a otras tablas:

~~~~ {.sql}
USE videoteca;

DROP TABLE IF EXISTS director;
CREATE TABLE director (
  id INTEGER UNSIGNED NOT NULL AUTO_INCREMENT,
  nombre VARCHAR(64) NOT NULL,
  apellidos VARCHAR(64) NOT NULL,
  imdb VARCHAR(32) NOT NULL DEFAULT '',
  PRIMARY KEY(id)
)
TYPE=InnoDB;

DROP TABLE IF EXISTS actor;
CREATE TABLE actor (
  id INTEGER UNSIGNED NOT NULL AUTO_INCREMENT,
  nombre VARCHAR(64) NOT NULL,
  apellidos VARCHAR(64) NOT NULL,
  imdb VARCHAR(32) NOT NULL DEFAULT '',
  PRIMARY KEY(id)
)
TYPE=InnoDB;
~~~~

Esta división puede ser llevada a extremos insospechados.

Nuestro ejemplo, finalizado
---------------------------

El código para la creación del modelo descrito al final de este capítulo es el siguiente:

~~~~ {.sql}
DROP DATABASE IF EXISTS videoteca;
CREATE DATABASE videoteca;

USE videoteca;

DROP TABLE IF EXISTS director;
CREATE TABLE director (
  id INTEGER UNSIGNED NOT NULL AUTO_INCREMENT,
  nombre VARCHAR(64) NOT NULL,
  apellidos VARCHAR(64) NOT NULL,
  imdb VARCHAR(32) NOT NULL DEFAULT '',
  PRIMARY KEY(id)
)
TYPE=InnoDB;

DROP TABLE IF EXISTS genero;
CREATE TABLE genero (
  id INTEGER UNSIGNED NOT NULL AUTO_INCREMENT,
  nombre VARCHAR(2) NOT NULL,
  descripcion VARCHAR(32) NOT NULL,
  PRIMARY KEY(id)
)
TYPE=InnoDB;

DROP TABLE IF EXISTS actor;
CREATE TABLE actor (
  id INTEGER UNSIGNED NOT NULL AUTO_INCREMENT,
  nombre VARCHAR(64) NOT NULL,
  apellidos VARCHAR(64) NOT NULL,
  imdb VARCHAR(32) NOT NULL DEFAULT '',
  PRIMARY KEY(id)
)
TYPE=InnoDB;

DROP TABLE IF EXISTS soporte;
CREATE TABLE soporte (
  id INTEGER UNSIGNED NOT NULL AUTO_INCREMENT,
  nombre VARCHAR(3) NOT NULL,
  descripcion VARCHAR(32) NOT NULL,
  PRIMARY KEY(id)
)
TYPE=InnoDB;

DROP TABLE IF EXISTS pelicula;
CREATE TABLE pelicula (
  id INTEGER UNSIGNED NOT NULL AUTO_INCREMENT,
  titulo VARCHAR(64) NOT NULL,
  idsoporte INTEGER UNSIGNED NOT NULL,
  idgenero INTEGER UNSIGNED NOT NULL,
  PRIMARY KEY(id),
  INDEX p_FK1(idsoporte),
  INDEX p_FK2(idgenero),
  FOREIGN KEY(idsoporte)
    REFERENCES soporte(id),
  FOREIGN KEY(idgenero)
    REFERENCES genero(id)
)
TYPE=InnoDB;

DROP TABLE IF EXISTS actores_por_pelicula;
CREATE TABLE actores_por_pelicula (
  idpelicula INTEGER UNSIGNED NOT NULL,
  idactor INTEGER UNSIGNED NOT NULL,
  PRIMARY KEY(idpelicula, idactor),
  INDEX app_FK1(idpelicula),
  INDEX app_FK2(idactor),
  FOREIGN KEY(idpelicula)
    REFERENCES pelicula(id),
  FOREIGN KEY(idactor)
    REFERENCES actor(id)
)
TYPE=InnoDB;

DROP TABLE IF EXISTS directores_por_pelicula;
CREATE TABLE directores_por_pelicula (
  idpelicula INTEGER UNSIGNED NOT NULL,
  iddirector INTEGER UNSIGNED NOT NULL,
  PRIMARY KEY(idpelicula, iddirector),
  INDEX dpp_FK1(idpelicula),
  INDEX dpp_FK2(iddirector),
  FOREIGN KEY(idpelicula)
    REFERENCES pelicula(id),
  FOREIGN KEY(iddirector)
    REFERENCES director(id)
)
TYPE=InnoDB;
~~~~

Las inserciones necesarias para poder trabajar con este modelo son las siguientes:

~~~~ {.sql}
USE videoteca;

-- Antes de realizar todas las inserciones,
-- borraremos todos los registros de las
-- tablas de la base de datos.
DELETE FROM actores_por_pelicula;
DELETE FROM directores_por_pelicula;
DELETE FROM actor;
DELETE FROM director;
DELETE FROM pelicula;
DELETE FROM soporte;
DELETE FROM genero;

-- Inserciones de soportes para las películas.
INSERT INTO soporte(nombre, descripcion)
VALUES('DVD', 'Digital Versatile Disc');

INSERT INTO soporte(nombre, descripcion)
VALUES('VHS', 'Video Home System');

-- Inserciones de géneros
INSERT INTO genero(nombre, descripcion)
VALUES('CF', 'Ciencia Ficción');

INSERT INTO genero(nombre, descripcion)
VALUES('A', 'Aventuras');

INSERT INTO genero(nombre, descripcion)
VALUES('D', 'Drama');

-- Inserciones de actores
INSERT INTO actor(nombre, apellidos, imdb)
VALUES('Harrison', 'Ford', 'nm0000148');

INSERT INTO actor(nombre, apellidos, imdb)
VALUES('Russell', 'Crowe', 'nm0000128');

-- Inserciones de directores
INSERT INTO director(nombre, apellidos, imdb)
VALUES('Ridley', 'Scott', 'nm0000631');

INSERT INTO director(nombre, apellidos, imdb)
VALUES('Mike', 'Nichols', 'nm0001566');

-- Inserciones de películas
INSERT INTO pelicula(titulo, idsoporte, idgenero)
VALUES('Blade Runner', 1, 1);

INSERT INTO pelicula(titulo, idsoporte, idgenero)
VALUES('Gladiator', 1, 2);

INSERT INTO pelicula(titulo, idsoporte, idgenero)
VALUES('A propósito de Henry', 2, 3);

-- Asociación de actores a cada película.
INSERT INTO actores_por_pelicula(
  idpelicula, idactor
)
VALUES(1, 1);

INSERT INTO actores_por_pelicula(
  idpelicula, idactor
)
VALUES(2, 2);

INSERT INTO actores_por_pelicula(
  idpelicula, idactor
)
VALUES(3, 1);

-- Asociación de directores por película.
INSERT INTO directores_por_pelicula(
  idpelicula, iddirector
)
VALUES(1, 1);

INSERT INTO directores_por_pelicula(
  idpelicula, iddirector
)
VALUES(2, 1);

INSERT INTO directores_por_pelicula(
  idpelicula, iddirector
)
VALUES(3, 2);
~~~~

Algunas inserciones más:

~~~~ {.sql}
USE videoteca;

-- Inserciones de soportes para las películas.
INSERT INTO soporte(nombre, descripcion)
VALUES('LD', 'Laser Disc');

-- Inserciones de géneros
INSERT INTO genero(nombre, descripcion)
VALUES('C', 'Comedia');

-- Inserciones de actores
INSERT INTO actor(nombre, apellidos, imdb)
VALUES('Lee', 'Marvin', 'nm0001511');

INSERT INTO actor(nombre, apellidos, imdb)
VALUES('Clint', 'Eastwood', 'nm0000142');

INSERT INTO actor(nombre, apellidos, imdb)
VALUES('Jean', 'Seberg', 'nm0781029');

INSERT INTO actor(nombre, apellidos, imdb)
VALUES('Bruce', 'Dern', 'nm0001136');

INSERT INTO actor(nombre, apellidos, imdb)
VALUES('Bruce', 'Boxleitner', 'nm0000310');

INSERT INTO actor(nombre, apellidos, imdb)
VALUES('Rutger', ' Hauer', 'nm0000442');

INSERT INTO actor(nombre,apellidos,imdb)
VALUES('Sean','Young','nm0000707');

INSERT INTO actor(nombre, apellidos, imdb)
VALUES('Matthew', 'McConaughey', 'nm0000190');

-- Inserciones de directores
INSERT INTO director(nombre, apellidos, imdb)
VALUES('Robert', 'Zemekis', 'nm0000709');

INSERT INTO director(nombre, apellidos, imdb)
VALUES('Douglas', 'Trumbull', 'nm0874320');

-- Inserciones de películas
INSERT INTO pelicula(titulo, idsoporte, idgenero)
VALUES('Naves misteriosas', 1, 1);
~~~~
