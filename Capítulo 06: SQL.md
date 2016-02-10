Creación de la base de datos e inserciones
------------------------------------------

Aunque la creación de la base de datos de ejemplo se describió en el capítulo anterior, la incluimos la principio de este como referencia:

~~~~ {.sql}
DROP DATABASE IF EXISTS videoteca;
CREATE DATABASE videoteca;

USE videoteca;

DROP TABLE IF EXISTS director;
CREATE TABLE director (
  id INT NOT NULL AUTO_INCREMENT,
  nombre VARCHAR(64) NOT NULL,
  apellidos VARCHAR(64) NOT NULL,
  imdb VARCHAR(32) NOT NULL DEFAULT '',
  PRIMARY KEY(id)
) ENGINE = InnoDB;

DROP TABLE IF EXISTS genero;
CREATE TABLE genero (
  id INT NOT NULL AUTO_INCREMENT,
  nombre VARCHAR(2) NOT NULL,
  descripcion VARCHAR(32) NOT NULL,
  PRIMARY KEY(id)
) ENGINE = InnoDB;

DROP TABLE IF EXISTS actor;
CREATE TABLE actor (
  id INT NOT NULL AUTO_INCREMENT,
  nombre VARCHAR(64) NOT NULL,
  apellidos VARCHAR(64) NOT NULL,
  imdb VARCHAR(32) NOT NULL DEFAULT '',
  PRIMARY KEY(id)
) ENGINE = InnoDB;

DROP TABLE IF EXISTS soporte;
CREATE TABLE soporte (
  id INT NOT NULL AUTO_INCREMENT,
  nombre VARCHAR(3) NOT NULL,
  descripcion VARCHAR(32) NOT NULL,
  PRIMARY KEY(id)
) ENGINE = InnoDB;

DROP TABLE IF EXISTS pelicula;
CREATE TABLE pelicula (
  id INT NOT NULL AUTO_INCREMENT,
  titulo VARCHAR(64) NOT NULL,
  idsoporte INT NOT NULL,
  idgenero INT NOT NULL,
  PRIMARY KEY(id),
  INDEX p_FK1(idsoporte),
  INDEX p_FK2(idgenero),
  FOREIGN KEY(idsoporte)
    REFERENCES soporte(id),
  FOREIGN KEY(idgenero)
    REFERENCES genero(id)
) ENGINE = InnoDB;

DROP TABLE IF EXISTS actores_por_pelicula;
CREATE TABLE actores_por_pelicula (
  idpelicula INT NOT NULL,
  idactor INT NOT NULL,
  PRIMARY KEY(idpelicula, idactor),
  INDEX app_FK1(idpelicula),
  INDEX app_FK2(idactor),
  FOREIGN KEY(idpelicula)
    REFERENCES pelicula(id),
  FOREIGN KEY(idactor)
    REFERENCES actor(id)
) ENGINE = InnoDB;

DROP TABLE IF EXISTS directores_por_pelicula;
CREATE TABLE directores_por_pelicula (
  idpelicula INT NOT NULL,
  iddirector INT NOT NULL,
  PRIMARY KEY(idpelicula, iddirector),
  INDEX dpp_FK1(idpelicula),
  INDEX dpp_FK2(iddirector),
  FOREIGN KEY(idpelicula)
    REFERENCES pelicula(id),
  FOREIGN KEY(iddirector)
    REFERENCES director(id)
) ENGINE = InnoDB;
~~~~

El primer bloque de inserciones necesarias para poder trabajar con este modelo es el siguiente:

~~~~ {.sql}
USE videoteca;

DELETE FROM actores_por_pelicula;
DELETE FROM directores_por_pelicula;
DELETE FROM actor;
DELETE FROM director;
DELETE FROM pelicula;
DELETE FROM soporte;
DELETE FROM genero;

INSERT INTO soporte(nombre, descripcion)
VALUES('DVD', 'Digital Versatile Disc');

INSERT INTO soporte(nombre, descripcion)
VALUES('VHS', 'Video Home System');

INSERT INTO genero(nombre, descripcion)
VALUES('CF', 'Ciencia Ficción');

INSERT INTO genero(nombre, descripcion)
VALUES('A', 'Aventuras');

INSERT INTO genero(nombre, descripcion)
VALUES('D', 'Drama');

INSERT INTO actor(nombre, apellidos, imdb)
VALUES('Harrison', 'Ford', 'nm0000148');

INSERT INTO actor(nombre, apellidos, imdb)
VALUES('Russell', 'Crowe', 'nm0000128');

INSERT INTO director(nombre, apellidos, imdb)
VALUES('Ridley', 'Scott', 'nm0000631');

INSERT INTO director(nombre, apellidos, imdb)
VALUES('Mike', 'Nichols', 'nm0001566');

INSERT INTO pelicula(titulo, idsoporte, idgenero)
VALUES('Blade Runner', 1, 1);

INSERT INTO pelicula(titulo, idsoporte, idgenero)
VALUES('Gladiator', 1, 2);

INSERT INTO pelicula(titulo, idsoporte, idgenero)
VALUES('A propósito de Henry', 2, 3);

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

A continuación, el resto de inserciones:

~~~~ {.sql}
USE videoteca;

INSERT INTO soporte(nombre, descripcion)
VALUES('LD', 'Laser Disc');

INSERT INTO genero(nombre, descripcion)
VALUES('C', 'Comedia');

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

INSERT INTO actor(nombre, apellidos, imdb)
VALUES('Sean', 'Young', 'nm0000707');

INSERT INTO actor(nombre, apellidos, imdb)
VALUES('Matthew', 'McConaughey', 'nm0000190');

INSERT INTO director(nombre, apellidos, imdb)
VALUES('Robert', 'Zemekis', 'nm0000709');

INSERT INTO director(nombre, apellidos, imdb)
VALUES('Douglas', 'Trumbull', 'nm0874320');

INSERT INTO pelicula(titulo, idsoporte, idgenero)
VALUES('Naves misteriosas', 1, 1);
~~~~

Operadores aritméticos
----------------------

En nuestras cuentas sobre papel, representamos las operaciones de suma, resta, multiplicación y división utilizando operadores los aritméticos +, -, \* y /, respectiva-mente. Con SQL no es diferente. Podemos realizar sumas con una simple consulta de selección:

~~~~ {.sql}
SELECT 2 + 2;
~~~~

Si en la tabla de películas almacenásemos el año en el que se estrenaron, podríamos saber cuántos años hace que se estrenó una determinada película con sólo restar al año actual el año de estreno. La consulta podría ser ésta:

~~~~ {.sql}
USE videoteca;

SELECT
  YEAR(CURDATE()) - estreno
FROM
  pelicula
WHERE
  id = 12;
~~~~

Siguiendo con el ejemplo anterior, la siguiente instrucción calcularía el tiempo pasado desde el estreno de todas las películas de la base de datos:

~~~~ {.sql}
USE videoteca;

SELECT
  YEAR(CURDATE()) - estreno
FROM
  pelicula;
~~~~

Vamos a crear una tabla para hacer pruebas con los operadores en la base de datos de pruebas. Para crear esa base de datos, si no existe en su servidor:

~~~~ {.sql}
DROP DATABASE IF EXISTS pruebas;
CREATE DATABASE pruebas;
~~~~

Ahora, para crear la tabla:

~~~~ {.sql}
USE pruebas;

DROP TABLE IF EXISTS operadores;
CREATE TABLE operadores(
  uno TINYINT UNSIGNED,
  otro TINYINT UNSIGNED
);
~~~~

Insertemos un registro:

~~~~ {.sql}
USE pruebas;

INSERT INTO operadores VALUES(3, 7);
~~~~

El resultado de sumar los valores almacenados en las dos columnas de esta tabla se consigue así:

~~~~ {.sql}
USE pruebas;
SELECT uno + otro FROM operadores;
~~~~

Obviamente, el resultado es 10. Pero, ¿cuál será el resultado de esta misma consulta, cuando insertemos esta nueva pareja de valores?

~~~~ {.sql}
USE pruebas;

INSERT INTO operadores VALUES(300, 500);
~~~~

Nos hemos salido de rango. Se trata de algo similar a lo que pasaría si intentásemos multiplicar dos valores enteros muy grandes, cuyo resultado estuviese por encima del máximo permitido por esa operación. Por ejemplo, tomemos el mayor número entero que podemos almacenar en MySQL y multipliquémoslo por 2:

~~~~ {.sql}
SELECT 18446744073709551615 * 2;
~~~~

El resultado: ese número menos uno. La razón, de nuevo, que hemos intentado almacenar en una columna un valor mayor que el que cabe en ella. Fíjese en que el resultado cambia si en lugar de multiplicar por 2 (entero) lo hacemos por 2,0 (coma flotante):

~~~~ {.sql}
SELECT 18446744073709551615 * 2.0;
~~~~

Operadores de comparación
-------------------------

Estos operadores nos permiten comparar dos valores. MySQL nos proporciona operadores para comprobar si dos valores son iguales (=) o diferentes (\<\>), si uno es mayor (\>) o menor (\<) que otro, y las variantes mayor o igual (\>=), menor o igual (\<=). Fíjese en los quebraderos de cabeza que nos puede dar NULL. Tanto es así, que hasta tenemos un operador de igualdad "a prueba de NULL": es el \<=\>. Probemos el operador de igualdad comparando dos parejas de valores:

~~~~ {.sql}
SELECT 7 = 7, NULL = NULL;
~~~~

Y ahora, el operador propio de NULL:

~~~~ {.sql}
SELECT 7 <=> 7, NULL <=> NULL;
~~~~

Podemos utilizar estos operadores para localizar elementos dentro de una tabla:

~~~~ {.sql}
USE videoteca;

SELECT * FROM actor WHERE apellidos = 'Crow';
~~~~

El operador LIKE permite realizar comparaciones con cadenas parciales, utilizando algunos caracteres especiales:

~~~~ {.sql}
SELECT
  *
FROM
  actor
WHERE
  apellidos LIKE 'Crow%';
~~~~

Si lo desea, puede utilizar el carácter del guión bajo para buscar concordancias con un solo carácter, por ejemplo:

~~~~ {.sql}
SELECT
  *
FROM
  actor
WHERE
  apellidos LIKE 'Crow_';
~~~~

Operadores lógicos
------------------

Están basados en las premisas de la lógica booleana, en la que las operaciones básicas son NOT, AND, OR y XOR. Y precisamente estos son los nombres de los operadores. Podemos probar su funcionamiento así:

~~~~ {.sql}
SELECT
  0 AND 0,
  0 AND 1,
  1 AND 0,
  1 AND 1
~~~~

Cambio de precedencia
---------------------

Se entiende por precedencia el orden en el que MySQL efectúa las operaciones de una determinada consulta. Puede comprobar cómo cambia ese orden utilizando paréntesis ejecutando la siguiente consulta:

~~~~ {.sql}
SELECT 2 + 2 * 5, (2 + 2) * 5;
~~~~

Listados de una tabla
---------------------

Veamos, ¿qué actores están dados de alta en nuestra base de datos? Seguro que sabe cómo obtener ese listado:

~~~~ {.sql}
USE videoteca;

SELECT * FROM actor;
~~~~

Podemos indicar qué campos queremos obtener, por ejemplo el identificador, el nombre y los apellidos:

~~~~ {.sql}
USE videoteca;

SELECT
  id,
  nombre,
  apellidos
FROM
  actor;
~~~~

Aunque también podemos concatenar nombre y apellidos en un solo campo:

~~~~ {.sql}
SELECT
  id,
  CONCAT(nombre, ' ', apellidos)
FROM
  actor;
~~~~

Esta última consulta tiene un problema "estético": el encabezado de la columna que muestra la concatenación. Para asignar un nombre a esa columna hemos de indicarlo tras la definición de la columna en la instrucción de selección:

~~~~ {.sql}
SELECT
  id,
  CONCAT(nombre, ' ', apellidos) AS nombre
FROM
  actor;
~~~~

Un posible cambio en esta consulta sería obtenerla ordenada por apellido y luego por nombre:

~~~~ {.sql}
SELECT
  id,
  CONCAT(nombre, ' ', apellidos) AS nombre
FROM
  actor
ORDER BY
  apellidos,
  nombre;
~~~~

Pero, ¿cómo ordenar el resultado usando un campo calculado (como la concatenación)? Así:

~~~~ {.sql}
SELECT
  id,
  CONCAT(nombre, ' ', apellidos) AS nombre
FROM
  actor
ORDER BY
  2;
~~~~

Otra consulta que le resultará muy útil es la que permite contar el número de elementos de elementos en una determinada tabla:

~~~~ {.sql}
SELECT
  COUNT(*)
FROM
  actor;
~~~~

Podemos utilizar el renombramiento de columnas:

~~~~ {.sql}
SELECT
  COUNT(*) actores
FROM
  actor;
~~~~

Listados de varias tablas
-------------------------

Para realizar consultas de selección sobre más de una tabla es preciso indicarle a MySQL de qué tablas se trata, y cómo están relacionadas esas tablas. De nada nos serviría hacer una consulta como esta:

~~~~ {.sql}
SELECT
  *
FROM
  pelicula,
  actores_por_pelicula,
  actor;
~~~~

Para poder organizar la información de estas tres tablas hemos de utilizar de forma diferentes la parte del FROM de la consulta, indicando ahí cómo se relacionan las tablas:

~~~~ {.sql}
SELECT
  titulo,
  CONCAT(nombre, ' ', apellidos) AS interprete
FROM
  pelicula p
  JOIN actores_por_pelicula app
    ON p.id =app.idpelicula
  JOIN actor a
    ON app.idactor = a.id;
~~~~

Vamos a incluir en esta consulta el director de la película:

~~~~ {.sql}
SELECT
  p.titulo,
  CONCAT(a.nombre, ' ', a.apellidos) AS interprete,
  CONCAT(d.nombre, ' ', d.apellidos) AS director
FROM
  pelicula p
  JOIN actores_por_pelicula app
    ON p.id = app.idpelicula
  JOIN actor a
    ON app.idactor = a.id
  JOIN directores_por_pelicula dpp
    ON p.id = dpp.idpelicula
  JOIN director d
    ON dpp.iddirector = d.id;
~~~~

La siguiente consulta nos devuelve el nombre del actor en una de las columnas y el número de películas en las que ha participado en la otra:

~~~~ {.sql}
SELECT
  CONCAT(a.apellidos, ', ', a.nombre) actor,
  COUNT(app.idpelicula) peliculas
FROM
  actor a
  JOIN actores_por_pelicula app
    ON a.id = app.idactor
GROUP BY
  a.id
ORDER BY
  a.apellidos,
  a.nombre;
~~~~

Pero, ¿y si queremos que aparezcan también los actores sin película en este listado? Podemos utilizar esta otra consulta:

~~~~ {.sql}
SELECT
  CONCAT(a.apellidos, ', ', a.nombre) actor,
  COUNT(app.idpelicula) peliculas
FROM
  actor a
  LEFT JOIN actores_por_pelicula app
    ON a.id = app.idactor
GROUP BY
  a.id
ORDER BY
  a.apellidos,
  a.nombre;
~~~~

Algunos de estos actores están dados de alta porque participan en una de las películas, pero aún no se les ha asociado a ella. Es el caso de Rutger Hauer y Sean Young, presentes en Blade Runner. Vamos a insertar los registros necesarios para que esa asociación exista. Primero necesitamos saber el identificador de la película Blade Runner:

~~~~ {.sql}
SELECT
  id
FROM
  pelicula
WHERE
  titulo = 'Blade Runner';
~~~~

Su identificador debería ser el 1. Ahora, los identificadores de los dos intérpretes:

~~~~ {.sql}
SELECT
  id
FROM
  actor
WHERE
  nombre = 'Rutger' AND
  apellidos = 'Hauer';
~~~~

Esta consulta nos... ¿no? ¿No devuelve nada? Pruebe a buscar sólo por el nombre, pero obtenga todos los campos, no sólo el identificador. Quizá haya un espacio antes del nombre. Intentémoslo con esta consulta:

~~~~ {.sql}
SELECT
  id
FROM
  actor
WHERE
  nombre= 'Rutger' AND
  apellidos = ' Hauer';
~~~~

Ahora sí, ¿verdad? El identificador de este actor es el 8. ¿Y el de Sean Young?

~~~~ {.sql}
SELECT
  id
FROM
  actor
WHERE
  nombre = 'Sean' AND
  apellidos = 'Young';
~~~~

En el siguiente apartado veremos cómo se modifican los valores almacenados en una tabla.

Actualizaciones
---------------

Necesitamos hacer un cambio en un determinado registro de la tabla de actores. Recuerde que el apellido de Rutger Hauer tenía un espacio al comienzo, algo que nos causó problemas en la sección anterior. Esta modificación se puede realizar de la siguiente forma:

~~~~ {.sql}
USE videoteca;

UPDATE
  actor
SET
  apellidos = 'Hauer'
WHERE
  nombre = 'Rutger';
~~~~

Podremos eliminar los espacios que no nos interesan de nombres y apellidos en las tablas de actores y directores utilizando la siguiente consulta:

~~~~ {.sql}
USE videoteca;

UPDATE
  actor a,
  director d
SET
  a.nombre = TRIM(a.nombre),
  a.apellidos = TRIM(a.apellidos),
  d.nombre = TRIM(d.nombre),
  d.apellidos = TRIM(d.apellidos);
~~~~

Inserciones
-----------

Vamos a insertar los valores necesarios en la tabla de actores por película para que Rutger Hauer y Sean Young aparezcan en el reparto de Blade Runner. Podemos insertar el primero así:

~~~~ {.sql}
INSERT INTO actores_por_pelicula(
  idpelicula, idactor
)
VALUES(1, 8);
~~~~

O podemos hacer esto otro:

~~~~ {.sql}
INSERT INTO actores_por_pelicula
VALUES(1, 9);
~~~~

La primera instrucción hace referencia a Rutger Hauer, la segunda a Sean Young. Veamos el efecto de estas inserciones, seleccionando la información de reparto de Blade Runner:

~~~~ {.sql}
USE videoteca;
SELECT
  titulo,
  CONCAT(nombre, ' ', apellidos) AS interprete
FROM
  pelicula p
  JOIN actores_por_pelicula app
    ON p.id = app.idpelicula
  JOIN actor a
    ON app.idactor = a.id
WHERE
  p.titulo = 'Blade Runner';
~~~~

También podríamos construir una consulta que cuente el número de actores que tiene cada película:

~~~~ {.sql}
USE videoteca;
SELECT
  p.titulo,
  COUNT(app.idactor) interpretes
FROM
  pelicula p
  LEFT JOIN actores_por_pelicula app
    ON p.id = app.idpelicula
GROUP BY
  p.id;
~~~~

Gracias a esta consulta de selección hemos localizado una inconsistencia en la base de datos: una película sin actores. ¿Existirá algún actor de esa película en la tabla de actores? Naves misteriosas fue protagonizada por Bruce Dern, entre otros. Si lo busca en la base de datos verá que aparece con el identificador 6. Para añadirlo como actor necesitamos saber el identificador de la película, que es el 4. Una nueva inserción en la tabla de actores por película solucionará el problema:

~~~~ {.sql}
INSERT INTO
  actores_por_pelicula(idpelicula, idactor)
VALUES(4, 6);
~~~~

Borrados
--------

Quizá necesitemos hacer algún borrado en nuestra base de datos. Comprobemos qué formatos de grabación están disponibles:

~~~~ {.sql}
USE videoteca;

SELECT
  *
FROM
  soporte;
~~~~

¿Tenemos películas en todos esos formatos? Podemos saberlo con la siguiente consulta:

~~~~ {.sql}
USE videoteca;

SELECT
  s.nombre,
  COUNT(p.id) peliculas
FROM
  soporte s
  LEFT JOIN pelicula p
    ON s.id = p.idsoporte
GROUP BY
  s.id;
~~~~

No existe ninguna película en formato LaserDisc. Eliminemos ese formato de nuestra tabla de soportes de grabación:

~~~~ {.sql}
DELETE FROM
  soporte
WHERE
  id = 3;
~~~~

Enlaces interesantes
--------------------

Puede encontrar más información sobre las consultas en la documentación de MySQL, en concreto [aquí](http://dev.mysql.com/doc/mysql/en/sql-syntax.html).
