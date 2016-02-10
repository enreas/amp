Base de datos reducida
----------------------

### Creación

~~~~ {.sql}
DROP DATABASE IF EXISTS videoteca;
CREATE DATABASE videoteca;

USE videoteca;

DROP TABLE IF EXISTS actor;
CREATE TABLE actor (
  id INT NOT NULL AUTO_INCREMENT,
  nombre VARCHAR(64) NOT NULL,
  apellidos VARCHAR(64) NOT NULL,
  imdb VARCHAR(32) NOT NULL DEFAULT '',
  PRIMARY KEY(id)
) ENGINE = InnoDB;
~~~~

### Inserción

~~~~ {.sql}
USE videoteca;

INSERT INTO actor(nombre, apellidos, imdb)
VALUES('Harrison', 'Ford', 'nm0000148');

INSERT INTO actor(nombre, apellidos, imdb)
VALUES('Russell', 'Crowe', 'nm0000128');
~~~~

Ejemplos
--------

### Lista de actores

~~~~ {.sql}
USE videoteca;

CREATE PROCEDURE pa_actores_lista()

SELECT
  *
FROM
  actor;
~~~~

Así se utiliza el procedimiento almacenado:

~~~~ {.sql}
USE videoteca;

CALL pa_actores_lista();
~~~~

El resultado es el mismo que se obtiene al ejecutar esta consulta:

~~~~ {.sql}
USE videoteca;

SELECT
  *
FROM
  actor;
~~~~

### Número de actores

~~~~ {.sql}
USE videoteca;

DELIMITER //

CREATE PROCEDURE pa_actores_cantidad2()
BEGIN

DECLARE actores INT;

SELECT
  COUNT(*)
FROM
  actor
INTO
  actores;
SELECT actores;

END

//
DELIMITER ;
~~~~

### Búsqueda de actores

~~~~ {.sql}
USE videoteca;

CREATE PROCEDURE pa_actores_buscar(letra CHAR(2))

SELECT
  *
FROM
  actor
WHERE
  nombre LIKE letra;
~~~~

Se utiliza así:

~~~~ {.sql}
USE videoteca;

CALL pa_actores_buscar('h%');
~~~~

### Lista y número de actores

~~~~ {.sql}
USE videoteca;

DELIMITER //

CREATE PROCEDURE pa_actores_buscar2(
  IN letra CHAR(2),
  OUT actores INT
)
BEGIN

SELECT
  *
FROM
  actor
WHERE
  nombre LIKE letra;

SELECT
  COUNT(*)
INTO
  actores
FROM
  actor
WHERE
  nombre LIKE letra;

END

//
DELIMITER ;
~~~~

Se utiliza así:

~~~~ {.sql}
USE videoteca;

CALL pa_actores_buscar2('h%', @cantidad);
SELECT @cantidad;
~~~~

### Funciones almacenadas

~~~~ {.sql}
USE videoteca;

DELIMITER //

CREATE FUNCTION fa_actores_cantidad()
RETURNS INT
BEGIN

DECLARE actores INT;

SELECT
  COUNT(*)
INTO
  actores
FROM
  actor;
RETURN actores;

END

//
DELIMITER ;
~~~~

Se utiliza así:

~~~~ {.sql}
USE videoteca;

SELECT fa_actores_cantidad();
~~~~

### Eliminación

~~~~ {.sql}
USE videoteca;

DROP PROCEDURE IF EXISTS pa_actores_lista;

CREATE PROCEDURE pa_actores_lista()

SELECT
  *
FROM
  actor
ORDER BY
  apellidos,
  nombre;
~~~~

Utilización desde PHP
---------------------

~~~~ {.php}
<?php

$videoteca = new mysqli(
  'localhost',
  'root',
  '',
  'videoteca'
);

if ($videoteca->errno != 0) {
  echo('Error en la conexión.');
  exit();
}

$resultado = $videoteca->query(
  'CALL pa_actores_lista()'
);

if ($resultado == FALSE){
  echo('Error en la consulta.');
}

while ($fila = $resultado->fetch_row()){
  printf(
    "(%u) %s %s: %s<br/>",
    $fila[0], $fila[1], $fila[2], $fila[3]
  );
}

$resultado->close();
$videoteca->close();

?>
~~~~
