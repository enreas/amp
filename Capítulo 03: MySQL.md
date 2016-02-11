Creación de bases de datos
--------------------------

El nombre la base de datos que utilizaremos para los ejemplos de este capítulo será <u>videoteca</u>, que podemos crear con las dos instrucciones siguientes:

~~~~ {.sql}
DROP DATABASE IF EXISTS videoteca;
CREATE DATABASE videoteca;
~~~~

Creación de tablas
------------------

La primera tabla que vamos a crear en nuestra base de datos contendrá información muy básica sobre algunas películas. Para crearla podemos utilizar las siguientes instrucciones:

~~~~ {.sql}
USE videoteca;

DROP TABLE IF EXISTS pelicula;
CREATE TABLE pelicula (
  titulo VARCHAR(64),
  director VARCHAR(128),
  actor VARCHAR(128)
);
~~~~

Si lo desea, puede realizar otras pruebas combinando la creación de bases de datos y tablas. Las siguientes instrucciones crean una base de datos de pruebas con una tabla llamada <u>persona</u>:

~~~~ {.sql}
DROP DATABASE IF EXISTS pruebas;
CREATE DATABASE pruebas;

USE pruebas;
CREATE TABLE persona(
  id INTEGER NOT NULL,
  nombre VARCHAR(16) NOT NULL,
  apellidos VARCHAR(64) NOT NULL DEFAULT ''
);
~~~~

Modificación de tablas
----------------------

Es posible modificar las características de las tablas después de crearlas. Por ejemplo, podemos modificar el tamaño del campo <u>nombre</u> de la tabla de pruebas que creamos anteriormente, así como añadir un nuevo campo que contenga la edad:

~~~~ {.sql}
ALTER TABLE persona
  MODIFY nombre VARCHAR(32) NOT NULL,
  ADD edad INTEGER NOT NULL;
~~~~

También es posible cambiar el nombre de la tabla:

~~~~ {.sql}
RENAME TABLE persona TO individuo;
~~~~

Borrado de tablas
-----------------

El borrado de una tabla es una operación muy sencilla, basta con indicar el nombre de la que queramos borrar:

~~~~ {.sql}
DROP TABLE IF EXISTS individuo;
~~~~

Transacciones
---------------------

Cree una tabla muy sencilla para los saldos de los clientes del banco dentro de la base de datos de pruebas utilizando las siguientes instrucciones:

~~~~ {.sql}
USE pruebas;

DROP TABLE IF EXISTS cuenta;
CREATE TABLE cuenta(
  idcliente INT NOT NULL,
  saldo DOUBLE NOT NULL DEFAULT 0
)
ENGINE = InnoDB;
~~~~

A continuación, inserte un par de valores para almacenar el saldo de dos clientes:

~~~~ {.sql}
USE pruebas;

INSERT INTO cuenta VALUES(212, 220.50);
INSERT INTO cuenta VALUES(555, 1200.00);
~~~~

Para realizar la transferencia han de completarse dos pasos: la cantidad transferida debe sumarse en la cuenta de destino y restarse de la cuenta que origina la transferencia. Una operación tras la otra:

~~~~ {.sql}
USE pruebas;

UPDATE
  cuenta
SET
  saldo = saldo + 300
WHERE
  idcliente = 212;

UPDATE
  cuenta
SET
  saldo = saldo - 300
WHERE
  idcliente = 555;
~~~~

La gestión de las transacciones se realiza mediante unas instrucciones muy sencillas. Siguiendo con el ejemplo, la transferencia se realizaría de la siguiente forma:

~~~~ {.sql}
USE pruebas;

START TRANSACTION;

UPDATE
  cuenta
SET
  saldo = saldo + 300
WHERE
  idcliente = 212;

UPDATE
  cuenta
SET
  saldo = saldo - 300
WHERE
  idcliente = 555;

COMMIT;
~~~~

Tipos de datos: texto
---------------------

Los campos de una determinada tabla pueden ser de diferente tipo, lo que determina la naturaleza de los datos que pueden contener. Puede tratarse de cadenas de texto. La siguiente tabla es un ejemplo de ello:

~~~~ {.sql}
USE pruebas;
DROP TABLE IF EXISTS texto;
CREATE TABLE texto(
  cadena VARCHAR(32)
);
~~~~

Podemos realizar las siguientes inserciones en esta tabla:

~~~~ {.sql}
USE pruebas;

INSERT INTO texto VALUES('d');
INSERT INTO texto VALUES('a');
INSERT INTO texto VALUES('A');
INSERT INTO texto VALUES('b');
~~~~

Estos valores pueden recuperarse con la consulta:

~~~~ {.sql}
USE PRUEBAS;

SELECT
  *
FROM
  texto
ORDER BY
  cadena;
~~~~

Como puede comprobar, los valores se devuelven ordenados alfabéticamente. Pero podemos crear la misma tabla así:

~~~~ {.sql}
CREATE TABLE texto(
  cadena VARCHAR(32) BINARY
);
~~~~

Si, tras realizar las mismas inserciones, realizamos la misma consulta que antes, el resultado no será el mismo. Esto se debe al uso del modificador <u>BINARY</u>. Por otra parte, las enumeraciones nos permiten determinar de antemano los valores que podrán insertarse en un campo de texto, no dando más opciones que las indicadas:

~~~~ {.sql}
USE pruebas;

DROP TABLE IF EXISTS pelicula;
CREATE TABLE pelicula(
  titulo VARCHAR(64),
  soporte ENUM('VHS', 'DVD')
);
~~~~

Estas dos inserciones serían válidas:

~~~~ {.sql}
USE pruebas;

INSERT INTO pelicula VALUES('Alien', 'VHS');
INSERT INTO pelicula VALUES('TRON', 'DVD');
~~~~

Sin embargo, esta tercera no:

~~~~ {.sql}
INSERT INTO pelicula VALUES(
  'Blade Runner', 'VCD'
);
~~~~

Aunque no se producirá ningún error, tampoco habrá inserción alguna. Puede comprobarlo realizando una consulta sobre esa tabla:

~~~~ {.sql}
SELECT
  *
FROM
  pelicula;
~~~~

Además de enumeraciones también podemos utilizar conjuntos. Son útiles en aquellos casos en los que podamos seleccionar entre varios valores más de uno. Por ejemplo, una película no siempre esta encasillada dentro de un sólo género, sino que puede formar parte de varios:

~~~~ {.sql}
USE pruebas;

DROP TABLE IF EXISTS pelicula;
CREATE TABLE pelicula(
  titulo VARCHAR(64),
  categoria SET(
    'ciencia-ficción','terror','thriller'
  )
);
~~~~

En este tipo de tablas las inserciones se realizan así:

~~~~ {.sql}
USE pruebas;
INSERT INTO pelicula VALUES(
  'Alien', 'ciencia-ficción,terror'
);
INSERT INTO pelicula VALUES(
  'Blade Runner', 'ciencia-ficción,thriller'
);
INSERT INTO pelicula VALUES(
  'Contact', 'ciencia-ficción'
);
~~~~

Tipos de datos: números
-----------------------

Los tipos de datos númericos permiten guardar números, como su propio nombre indica. Por un lado, podemos guardar números enteros con signo:

~~~~ {.sql}
USE pruebas;

DROP TABLE IF EXISTS enteros;
CREATE TABLE enteros(entero INTEGER);
~~~~

Podemos limitar más los tipos de datos que permitidos, limitándolos a números enteros sin signo:

~~~~ {.sql}
USE pruebas;

DROP TABLE IF EXISTS enteros;
CREATE TABLE enteros(entero INTEGER UNSIGNED);
~~~~

Además, es posible indicar si queremos que MySQL rellene con ceros a la izquierda los valores que introduzcamos, para lo que también hemos de indicar cuántos dígitos de ancho tendrán esos números:

~~~~ {.sql}
USE pruebas;

DROP TABLE IF EXISTS enteros;
CREATE TABLE enteros(
  entero INTEGER(4) UNSIGNED ZEROFILL
);
~~~~

Tras realizar estas inserciones:

~~~~ {.sql}
USE pruebas;

INSERT INTO enteros values(1);
INSERT INTO enteros values(10);
INSERT INTO enteros values(100);
INSERT INTO enteros values(1000);
~~~~

podemos ver el resultado al realizar una consulta:

~~~~ {.sql}
USE pruebas;
SELECT * FROM enteros;
~~~~

Podemos insertar valores de un ancho mayor que el definido al crear la tabla:

~~~~ {.sql}
INSERT INTO enteros VALUES(10000);
~~~~

Compruebe usted mismo el resultado de consultar ahora los datos de la tabla. MySQL también nos permite utilizar números decimales en los campos de las tablas.

Tipos de datos: números en coma flotante
----------------------------------------

~~~~ {.sql}
USE pruebas;

DROP TABLE IF EXISTS decimales;
CREATE TABLE decimales(
  precio FLOAT(6,2)
);

Como vimos anteriormente, se pueden insertar valores entre -9999,99 y 9999,99. Inserte algunos y vea el resultado de una consulta de selección:

USE pruebas;

INSERT INTO decimales VALUES(29.99);
INSERT INTO decimales VALUES(30);
INSERT INTO decimales VALUES(199.994);
INSERT INTO decimales VALUES(199.995);

SELECT * FROM decimales;
~~~~

Tipos de datos: fechas y horas
------------------------------

Podemos crear campos en nuestras tablas para contener fechas y horas. Por ejemplo, la siguiente tabla contiene un campo cuyo valor contendrá la fecha en la que se modificó el registro:

~~~~ {.sql}
USE pruebas;

DROP TABLE IF EXISTS pelicula;
CREATE TABLE pelicula(
  titulo VARCHAR(64),
  modificado TIMESTAMP
);
~~~~

Podemos realizar una inserción en la tabla:

~~~~ {.sql}
INSERT INTO pelicula(titulo) VALUES('Alieni');
~~~~

Si hace una consulta de selección sobre esa tabla podrá comprobar que la columna <u>modificado</u> contiene la fecha en la que se realizó la inserción. Ahora, vamos a corregir el error del nombre de la película:

~~~~ {.sql}
USE pruebas;

UPDATE
  pelicula
SET
  titulo = 'Alien'
WHERE
  titulo = 'Alieni';
~~~~

Si vuelve a realizar unan consulta sobre la misma tabla comprobará que el valor de la columna <u>modificado</u> ha cambiado.

Enlaces interesantes
--------------------

La [documentación de MySQL](http://dev.mysql.com/doc/mysql/en/index.html) contiene una descripción pormenorizada de todos los tipos de datos que permite utilizar. En concreto, se trata de [esta sección](http://dev.mysql.com/doc/mysql/en/column-types.html).
