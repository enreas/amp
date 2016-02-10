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

### Inserción

~~~~ {.sql}
USE videoteca;

DELIMITER //

CREATE TRIGGER dc_actor_insertar
AFTER INSERT ON actor
FOR EACH ROW BEGIN

DECLARE nuevo_carpeta VARCHAR(255);

SET nuevo_carpeta = CONCAT(
  LOWER(NEW.nombre),
  '_',
  LOWER(NEW.apellidos)
);

INSERT INTO actor_ext(idactor, imdb, carpeta)
VALUES(NEW.id, '', nuevo_carpeta);

END

//
DELIMITER ;
~~~~

Pruebe a insertar un nuevo registro:

~~~~ {.sql}
USE videoteca;

INSERT INTO actor(nombre, apellidos)
VALUES('Tim','Robins');
~~~~

### Actualización

Resolución del error:

~~~~ {.sql}
USE videoteca;

UPDATE
  actor
SET
  apellidos = 'Robbins'
WHERE
  id = 3;

USE videoteca;

UPDATE
  actor_ext
SET
  carpeta = 'tim_robbins'
WHERE
  idactor = 3;
~~~~

Desencadenador que actualiza ambas tablas:

~~~~ {.sql}
USE videoteca;

DELIMITER //

CREATE TRIGGER dc_actor_actualizar
AFTER UPDATE ON actor
FOR EACH ROW BEGIN

DECLARE nuevo_carpeta VARCHAR(255);

SET nuevo_carpeta = CONCAT(
  LOWER(NEW.nombre),
  '_',
  LOWER(NEW.apellidos)
);

UPDATE
  actor_ext
SET
  carpeta = nuevo_carpeta
WHERE
  idactor = NEW.id;

END

//
DELIMITER ;
~~~~

### Borrado

Eliminación de un registro:

~~~~ {.sql}
USE videoteca;

DELETE FROM actor WHERE id = 3;
~~~~

Desencadenador de borrado:

~~~~ {.sql}
USE videoteca;

DELIMITER //

CREATE TRIGGER dc_actor_eliminar
BEFORE DELETE ON actor
FOR EACH ROW BEGIN

DELETE FROM
  actor_ext
WHERE
  idactor = OLD.id;

END

//
DELIMITER ;
~~~~
