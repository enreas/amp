Bases de datos de ejemplo
-------------------------

### Creación

~~~~ {.sql}
DROP DATABASE IF EXISTS videoteca;
CREATE DATABASE videoteca;

USE videoteca;

CREATE TABLE actor (
  id INT NOT NULL
    AUTO_INCREMENT,
  nombre VARCHAR(64) NOT NULL,
  apellidos VARCHAR(64) NOT NULL,
  PRIMARY KEY(id)
) ENGINE = InnoDB;

CREATE TABLE actor_ext (
  idactor INT NOT NULL,
  imdb VARCHAR(32) NOT NULL,
  carpeta VARCHAR(255) NOT NULL,
  PRIMARY KEY(idactor),
  INDEX ae_FK(idactor),
  FOREIGN KEY(idactor)
    REFERENCES actor(id)
) ENGINE = InnoDB;
~~~~

### Inserción

~~~~ {.sql}
USE videoteca;

INSERT INTO actor(nombre, apellidos)
VALUES('Harrison', 'Ford');

INSERT INTO actor(nombre, apellidos)
VALUES('Russell', 'Crowe');

INSERT INTO actor_ext(idactor, imdb, carpeta)
VALUES(1, 'nm0000148', 'harrison_ford');

INSERT INTO actor_ext(idactor, imdb, carpeta)
VALUES(2, 'nm0000128', 'russell_crowe');
~~~~

Ejemplos
--------

### Creación

Obtención de los datos contenidos en ambas tablas:

~~~~ {.sql}
USE videoteca;

SELECT
  a.id,
  a.nombre,
  a.apellidos,
  ae.imdb
FROM
  actor a
  JOIN actor_ext ae ON a.id = ae.idactor;
~~~~

Una vista que proporciona el mismo resultado:

~~~~ {.sql}
USE videoteca;

CREATE VIEW v_actor AS

SELECT
  a.id,
  a.nombre,
  a.apellidos,
  ae.imdb
FROM
  actor a
  JOIN actor_ext ae ON a.id = ae.idactor;
~~~~

Las vistas pueden utilizarse como si de tablas se tratase:

~~~~ {.sql}
USE videoteca;

SELECT * FROM v_actor;
~~~~

### Modificación

Las vistas se comportan como tablas:

~~~~ {.sql}
USE videoteca;

SELECT
  *
FROM
  v_actor
ORDER BY
  apellidos,
  nombre;
~~~~

Modifiquemos el código de la vista:

~~~~ {.sql}
USE videoteca;

CREATE OR REPLACE VIEW v_actor AS

SELECT
  a.id,
  a.nombre,
  a.apellidos,
  ae.imdb
FROM
  actor a
  JOIN actor_ext ae ON a.id = ae.idactor
ORDER BY
  apellidos,
  nombre;
~~~~

Puede obtenerse el mismo resultado así:

~~~~ {.sql}
USE videoteca;

ALTER VIEW v_actor AS

SELECT
  a.id,
  a.nombre,
  a.apellidos,
  ae.imdb
FROM
  actor a
  JOIN actor_ext ae ON a.id = ae.idactor
ORDER BY
  apellidos,
  nombre;
~~~~

### Eliminación

Elimine así las vistas:

~~~~ {.sql}
USE videoteca;

DROP VIEW v_actor;
~~~~

Utilización desde PHP
---------------------

Éste es el código necesario para utilizar la vista que devuelve la lista de intérpretes ordenados por nombre:

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
