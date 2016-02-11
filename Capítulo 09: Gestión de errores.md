Errores de conexión
-------------------

En la siguiente página hemos indicado, a propósito, una clave de acceso incorrecta para el usuario <u>root</u>:

~~~~ {.php}
<?php

$videoteca = new mysqli(
  'localhost',
  'usuario',
  '',
  'videoteca'
);

if ($videoteca->errno != 0){
  echo('Error en la conexión.');
  exit();
}

$resultado = $videoteca->query(
  'SELECT * FROM genero'
);

if ($resultado == FALSE){
  echo('Error en la consulta.');
}

while ($fila = $resultado->fetch_row()){
  printf(
    "(%u) %s - %s<br/>",
    $fila[0], $fila[1], $fila[2]
  );
  print_r($resultado->fetch_field());
}

$resultado->close();
$videoteca->close();

?>
~~~~

Sin embargo, en lugar de sobre un error en la conexión, se avisa de un error en la consulta. Esto se subsana con este programa:

~~~~ {.php}
<?php

$videoteca = new mysqli(
  'localhost',
  'usuario',
  '',
  'videoteca'
);

if (mysqli_connect_errno()){
  echo('Error en la conexión.');
  exit();
}

$resultado = $videoteca->query(
  'SELECT * FROM genero'
);

if ($resultado == FALSE){
  echo('Error en la consulta.');
}

while ($fila = $resultado->fetch_row()){
  printf(
    "(%u) %s - %s<br/>",
    $fila[0], $fila[1], $fila[2]
  );
  print_r($resultado->fetch_field());
}

$resultado->close();
$videoteca->close();

?>
~~~~

Informe de errores
------------------

Si lo deseamos podemos generar nuestros propios errores:

~~~~ {.php}
<?php

$sMensaje =
  'El nombre y la descripción '.
  'del nuevo género no pueden '.
  'ser cadenas vacías.';
trigger_error($sMensaje, E_USER_ERROR);

?>
~~~~

También podemos crear nuestras propias funciones para la gestión del informe de errores:

~~~~ {.php}
<?php

function informar_error(
  $codigo, $descripcion, $archivo, $linea
){

  printf("Ha ocurrido un error.<br/>");
  printf("<b>Código:</b> %i.<br/>", $codigo);
  printf("<b>Descripción:</b> %s.<br/>", $descripcion);
  printf("<b>Archivo:</b> %s.<br/>", $archivo);
  printf("<b>Línea:</b> %i.<br/>", $linea);
/*
  error_log(
    "Error.",
    1,
    "videoteca@example.com",
    "Subject: \nFrom: Servidor en produccion\n"
  );
*/

}

set_error_handler("informar_error");

$sMensaje =
  'El nombre y la descripción '.
  'del nuevo género no pueden '.
  'ser cadenas vacías';
trigger_error($sMensaje, E_USER_ERROR);

?>
~~~~

Enlaces interesantes
--------------------

La documentación de PHP hace referencia a la gestión de errores [aquí](http://www.php.net/manual/en/ref.errorfunc.php).
