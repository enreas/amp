Envío de datos
--------------

Vamos a crear un formulario para enviar a otra página la información necesaria para dar de alta un nuevo género cinematográfico. El código es el siguiente:

~~~~ {.html4strict}
<html>

  <head>
    <title>Nuevo género</title>
  </head>

  <body>

    <form name="frm_genero" method="get" action="">

      <strong>Nombre:</strong><br>
      <input type="text" name="nombre"><br>

      <strong>Descripción:</strong><br>
      <input type="text" name="descripcion"><br>

      <input type="submit" value="Guardar">

    </form>

  </body>

</html>
~~~~

Recepción de datos
------------------

La siguiente página se limita a recibir los datos enviados por la página de la sección anterior. Asegúrese de que el nombre con el que guarde esta página esté en el campo [u]action[/u] de la página anterior y de que ambas están en la misma carpeta:

~~~~ {.php}
<?php

$pParametros = FALSE;
if(count($_GET) != 0){
  $pParametros = $_GET;
}elseif(count($_POST) != 0){
  $pParametros = $_POST;
}

if($pParametros == FALSE){
  echo('No se ha pasado valor alguno.');
  exit();
}

echo('<table border="1">');
echo('<tr>');
echo('<th>Nombre</th><th>Valor</th>');
echo('</tr>');

foreach($pParametros as $nombre => $valor){
  echo('<tr>');
  printf(
    "<td>%s</td><td>%s</td>",
    $nombre,$valor);
  echo('</tr>');
}

echo('</table>');

?>
~~~~

Inserción de registros
----------------------

Vamos a combinar las dos páginas de las secciones anteriores en una sola que, además, guarde esos valores en la tabla de géneros de la base de datos de películas que hemos creado:

~~~~ {.php}
<?php

$pParametros = FALSE;
if(count($_GET) != 0){
  $pParametros = $_GET;
}elseif(count($_POST) != 0){
  $pParametros = $_POST;
}
if($pParametros != FALSE){

  $sNombre = $pParametros['nombre'];
  $sDescripcion = $pParametros['descripcion'];

  if(($sNombre == '') or ($sDescripcion == '')){
    $sMensaje =
      'El nombre y la descripción '.
      'del nuevo género no pueden '.
      'ser cadenas vacías.';
    echo($sMensaje);
  }else{

$sConsulta = <<<CONSULTA
INSERT INTO genero(
  nombre,
  descripcion
)VALUES(
  '$sNombre',
  '$sDescripcion'
);
CONSULTA;

    $videoteca = new mysqli('', '', '', 'videote-ca');

    if ($videoteca == FALSE){
      echo('Error en la conexión.');
      exit();
    }

    $resultado = $videoteca->query($sConsulta);

    if($resultado == FALSE){
      echo('Error en la consulta.');
      echo($videoteca->error);
      exit();
    }

    echo('Nuevo género añadido.<hr/>');

    $videoteca->close();

  }

}

?>

<html>

  <head>
    <title>Nuevo género</title>
  </head>

  <body>

    <form name="frm_genero" method="get" action="">

      <strong>Nombre:</strong><br>
      <input type="text" name="nombre"><br>

      <strong>Descripción:</strong><br>
      <input type="text" name="descripcion"><br>

      <input type="submit" value="Guardar">

    </form>

  </body>

</html>
~~~~

Control en el lado del cliente
------------------------------

Para impedir que se intenten realizar inserciones en las que falten el nombre o de la descripción del género vamos a utilizar el siguiente JavaScript:

~~~~ {.javascript}
function comprobar_valores(){

  var frm = document.frm_genero;
  var sNombre = frm.nombre.value;
  var sDescripcion = frm.descripcion.value;

  if(sNombre == ''){
    alert('Falta el nombre.');
    frm.nombre.select();
    return(false);
  }

  if(sDescripcion == ''){
    alert('Falta la descripción.');
    frm.descripcion.select();
    return(false);
  }

  return(true);

}
~~~~

Lo utilizaremos desde la página PHP así:

~~~~ {.html4strict}
<html>

  <head>
    <title>Nuevo género</title>
    <script
      language="javascript"
      src="genero.js">
      <!--
      -->
    </script>
  </head>

  <body>

    <form name="frm_genero" method="get" action="">

      <strong>Nombre:</strong><br>
      <input type="text" name="nombre"><br>

      <strong>Descripción:</strong><br>
      <input type="text" name="descripcion"><br>

      <input type="submit" value="Guardar">

    </form>

  </body>

</html>
~~~~

Enlaces interesantes
--------------------

Si quiere profundizar tanto en el funcionamiento de los formularios como en el uso de JavaScript puede que encuentre interesantes estos enlaces:

-   [Formularios](http://www.w3.org/TR/html4/interact/forms.html).
-   [JavaScript, según Mozilla](http://www.mozilla.org/js/).
-   [JavaScript, según Microsoft](http://msdn.microsoft.com/library/default.asp?url=/library/en-us/script56/html/js56jsoriJScript.asp).
