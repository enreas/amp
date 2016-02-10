Conexión
--------

Podemos realizar la conexión con MySQL así:

~~~~ {.php}
<?php

$conexion = mysqli_connect(
  'localhost',
  'root',
  'clave',
  'videoteca'
);

if ($conexion == FALSE){
  echo('Error en la conexión.');
  exit();
}

...
~~~~

Pero también usando la versión orientada a objetos:

~~~~ {.php}
<?php

$videoteca = new mysqli(
  'localhost',
  'root',
  'clave',
  'videoteca'
);

if ($videoteca->errno != 0) {
  echo('Error en la conexión.');
  exit();
}

...
~~~~

Operación
---------

Este fragmento de código permite obtener la lista de géneros cinematográficos que hay en la base de datos:

~~~~ {.php}
...
$resultado = mysqli_query(
  $conexion,
  'SELECT * FROM genero'
);

if ($resultado == FALSE){
  echo('Error en la consulta.');
}
...
~~~~

Podemos recuperar esos registros utilizando el siguiente código:

~~~~ {.php}
...
while($fila = mysqli_fetch_row($resultado)){
  printf(
    "(%u) %s - %s<br/>",
    $fila[0], $fila[1], $fila[2]
  );
}
...
~~~~

La versión orientada a objetos sería la siguiente:

~~~~ {.php}
...
$resultado = $videoteca->query(
  'SELECT * FROM genero'
);

if ($resultado == FALSE){
  echo('Error en la consulta.');
}


while ($fila = $resultado->fetch_row()) {
  printf(
    "(%u) %s - %s<br/>",
    $fila[0], $fila[1], $fila[2]
  );
}
...
~~~~

Desconexión
-----------

La desconexión es tan sencilla como esto:

~~~~ {.php}
...

mysqli_free_result($resultado);
mysqli_close($conexion);

?>
~~~~

El mismo resultado puede obtenerse con las siguientes líneas, utilizando la versión orientada a objetos de MySQLi:

~~~~ {.php}
...

$resultado->close();
$videoteca->close();

?>
~~~~

Ejemplo completo
----------------

La versión orientada a objetos completa del ejemplo es la siguiente:

~~~~ {.php}
<?php

$videoteca = new mysqli(
  'localhost',
  'root',
  'clave',
  'videoteca'
);

if ($videoteca->errno != 0) {
  echo('Error en la conexión.');
  exit();
}

$resultado = $videoteca->query(
  'SELECT * FROM genero'
);

if ($resultado == FALSE){
  echo('Error en la consulta.');
}


while ($fila = $resultado->fetch_row()) {
  printf(
    "(%u) %s - %s<br/>",
    $fila[0], $fila[1], $fila[2]
  );
}

$resultado->close();
$videoteca->close();

?>
~~~~

Enlaces interesantes
--------------------

Para este capítulo seguro que encuentra interesante:

-   la documentación de [MySQLi](http://www.php.net/manual/en/ref.mysqli.php).
-   la documentación de las opciones de [configuración de PHP](http://www.php.net/manual/en/ini.php).
-   la documentación de las opciones de [configuración de Apache](http://www.php.net/configuration.changes).
