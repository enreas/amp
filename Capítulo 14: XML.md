XML de ejemplo
--------------

El siguiente documento XML contiene detalles sobre el reparto de Blade Runner:

~~~~ {.xml}
<?xml version="1.0"?>
<reparto>
  <papel protagonista="si">
    <personaje>Rick Deckard</personaje>
    <actor>Harrison Ford</actor>
  </papel>
  <papel protagonista="no">
    <personaje>Roy Batty</personaje>
    <actor>Rutger Hauer</actor>
  </papel>
  <papel protagonista="no">
    <personaje>Rachael</personaje>
    <actor>Sean Young</actor>
  </papel>
</reparto>
~~~~

MySQL y XML desde la línea de órdenes
-------------------------------------

Así puede conectar con el servidor MySQL desde la línea de órdenes de forma que el resultado de las consultas esté en formato XML:

~~~~ {.bash}
mysql -u root -p --xml
~~~~

Puede comprobarlo con la siguiente consulta:

~~~~ {.sql}
USE videoteca;

SELECT * FROM soporte;
~~~~

O con ésta:

~~~~ {.sql}
USE videoteca;

SELECT nombre FROM soporte;
~~~~

O con ésta otra consulta:

~~~~ {.sql}
USE videoteca;

SELECT nombre AS soporte FROM soporte;
~~~~

La siguiente orden le permitirá guardar en formato XML, en un archivo de texto llamado <u>soportes.xml</u>, el resultado de una consulta SQL:

~~~~ {.bash}
mysql -u root -p --xml -e "SELECT * FROM soporte" videoteca > soportes.xml
~~~~

Funciones XML en MySQL
----------------------

MySQL proporciona dos funciones para trabajar con documentos XML: <u>ExtractValue</u> y <u>UpdateXML</u>. Para jugar con estas funciones utilizaremos la tabla <u>pelicula</u>, que se creaba así:

~~~~ {.sql}
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
~~~~

Vamos a modificarla, añadiendo un campo más:

~~~~ {.sql}
USE videoteca;

ALTER TABLE
  pelicula
ADD
  reparto TEXT NOT NULL;
~~~~

Ahora, modifiquemos la entrada perteneciente a 'Blade Runner':

~~~~ {.sql}
USE videoteca;

UPDATE
  pelicula
SET
  reparto =
   '<reparto>
      <papel protagonista="si">
        <personaje>Rick Deckard</personaje>
        <actor>Harrisond Ford</actor>
      </papel>
      <papel protagonista="no">
        <personaje>Roy Batty</personaje>
        <actor>Rutger Hauer</actor>
      </papel>
      <papel protagonista="no">
        <personaje>Rachael</personaje>
        <actor>Sean Young</actor>
      </papel>
    </reparto>'
WHERE
  id = 1;
~~~~

### ExtractValue

Un ejemplo de funcionamiento de la función <u>ExtractValue</u>:

~~~~ {.sql}
SELECT ExtractValue(
 '<papel>
    <personaje>Roy Batty</personaje>
    <actor>Rutger Hauer</actor>
  </papel>',
  '/papel/personaje'
) AS personaje;
~~~~

Otro ejemplo más:

~~~~ {.sql}
USE videoteca;

SELECT
  ExtractValue(
    reparto,
    '/reparto/papel/personaje'
  ) AS personajes
FROM
  pelicula
WHERE
  id = 1;
~~~~

Un procedimiento almacenado para extraer datos de un documento XML:

~~~~ {.sql}
DELIMITER //

DROP PROCEDURE IF EXISTS pa_personajes_obtener;
CREATE PROCEDURE pa_personajes_obtener (
  idpelicula INT
)
BEGIN

DECLARE i INT DEFAULT 1;
DECLARE j INT DEFAULT 0;

DECLARE personajes TEXT DEFAULT '';
DECLARE personaje TEXT DEFAULT '';

SELECT
  ExtractValue(reparto, 'count(/reparto/papel)')
INTO
  j
FROM
  pelicula
WHERE
  id = idpelicula;

WHILE i <= j DO

  SELECT
    ExtractValue(
      reparto,
      '/reparto/papel[$i]/personaje')
  INTO
    personaje
  FROM
    pelicula
  WHERE
    id = idpelicula;

  SELECT
    IF(
      i <> j,
      CONCAT(personaje, ', '),
      personaje)
  INTO personaje;

  SET personajes = CONCAT(personajes, personaje);

  SET i = i + 1;

  END WHILE;

  SELECT personajes;

END //

DELIMITER ;
~~~~

Es posible utilizar este procedimiento almacenado así:

~~~~ {.sql}
USE videoteca;

CALL pa_personajes_obtener(1);
~~~~

### UpdateXML

Corrección del error del apartado anterior:

~~~~ {.sql}
USE videoteca;

UPDATE
  pelicula
SET
  reparto = UpdateXML(
    reparto,
    '/reparto/papel[1]/actor',
    '<actor>Harrison Ford</actor>'
  )
WHERE
  id = 1;
~~~~

Comprobemos si el cambio ha tenido éxito:

~~~~ {.sql}
USE videoteca;

SELECT
  ExtractValue(
    reparto,
    '/reparto/papel[1]/actor'
  ) AS actor
FROM
  pelicula
WHERE
  id = 1;
~~~~

SimpleXML
---------

Recordemos cómo guardar en un archivo de texto, en formato XML, el resultado de una consulta SQL:

~~~~ {.bash}
mysql -u root -p --xml -e "SELECT * FROM soporte" videoteca > soportes.xml
~~~~

El contenido de este archivo será algo parecido a esto:

~~~~ {.xml}
<?xml version="1.0"?>

<resultset statement="SELECT * FROM soporte
" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <row>
    <field name="id">1</field>
    <field name="nombre">DVD</field>
    <field name="descripcion">Digital Versatile Disc</field>
  </row>

  <row>
    <field name="id">2</field>
    <field name="nombre">VHS</field>
    <field name="descripcion">Video Home Sys-tem</field>
  </row>
</resultset>
~~~~

### Carga

El siguiente código PHP permite cargar un documento XML:

~~~~ {.php}
<?php

$xml = new SimpleXMLElement(
  'C:\soportes.xml', NULL, TRUE
);

echo $xml->asXML();

?>
~~~~

### Recorrido de nodos

~~~~ {.php}
<?php

$xml = new SimpleXMLElement(
  'C:\soportes.xml', NULL, TRUE
);

$rows = $xml->xpath('/resultset/row');

foreach($rows as $row){
  echo $row->asXML() . '<br/>';
}

?>
~~~~

### Acceso a atributos

~~~~ {.php}
<?php

$xml = new SimpleXMLElement(
  'C:\soportes.xml', NULL, TRUE
);

$rows = $xml->xpath('/resultset/row');

foreach($rows as $row){

  $fields = $row->xpath('field');

  foreach($fields as $field){

    $attributes = $field->attributes();

    foreach($attributes as $attribute){

      switch($attribute){

        case 'id':
          echo('(' . $field->asXML() . ') ');
          break;

        case 'nombre':
          echo('<b>' . $field->asXML() . '</b>: ');
          break;

        case 'descripcion':
          echo($field->asXML() . '.<br/>');
          break;

      }

    }

  }

}

?>
~~~~
