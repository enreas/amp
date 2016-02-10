La hora con Smarty
------------------

Para recrear el primer programa que hicimos con PHP usando Smarty necesitamos crear una plantilla:

~~~~ {.smarty}
La hora es: {$hora}.
~~~~

Posteriormente, utilizaremos esa plantilla desde PHP:

~~~~ {.php}
<?php

include('Smarty.class.php');

$smarty = new Smarty;
$smarty->assign('hora', date("H:i:s"));
$smarty->display('smarty.tpl');

?>
~~~~

Cambie <u>smarty.tpl</u> por el nombre del archivo con el que haya guardado la plantilla.

Lista de géneros
----------------

Podemos utilizar plantillas para mostrar la lista de los géneros disponibles en nuestra base de datos de películas. Ésta sería la plantilla:

~~~~ {.smarty}
<html>

  <head>
    <title>Smarty - géneros</title>
  </head>

  <body>
    {section name=i loop=$generos}
      ({$generos[i].id}) {$generos[i].nombre} -
      {$generos[i].descripcion}<br/>
    {/section}
  </body>

</html>
~~~~

Y éste el código de la página PHP:

~~~~ {.php}
<?php

include('Smarty.class.php');

$videoteca = new mysqli('', '', '', 'videoteca');

if (mysqli_connect_errno() != 0) {
  echo('Error en la conexión.');
  exit();
}

$resultado = $videoteca->query(
  'SELECT * FROM genero'
);

if ($resultado == FALSE){
  echo('Error en la consulta.');
}

$generos = array();

while ($fila = $resultado->fetch_assoc()){
  array_push($generos, $fila);
}

$resultado->close();
$videoteca->close();

$smarty = new Smarty;
$smarty->assign('generos', $generos);
$smarty->display('generos.tpl');

?>
~~~~

Lista de géneros... otra vez
----------------------------

Hagamos un pequeño cambio en la página PHP que muestra los géneros utilizando Smarty. Vamos a recibir el nombre de la plantilla a través de la URL. Si no se recibe ningún nombre, utilizaremos uno predeterminado. La plantilla sería:

~~~~ {.smarty}
<html>

  <head>
    <title>Smarty - géneros</title>
  </head>

  <body>

    <table border="1">

      <tr>
        <th>Id</th>
        <th>Nombre</th>
        <th>Descripción</th>
      </tr>

      {section name=i loop=$generos}
      <tr>
        <td>{$generos[i].id}</td>
        <td>{$generos[i].nombre}</td>
        <td>{$generos[i].descripcion}</td>
      </tr>
      {/section}
    </table>

   </body>

</html>
~~~~

Y el código de la página PHP:

~~~~ {.php}
<?php

include('Smarty.class.php');

$plantilla = $_GET['plantilla'];
if($plantilla == ''){
  $plantilla = 'generos.tpl';
}

$videoteca = new mysqli('','','','videoteca');

if (mysqli_connect_errno() != 0) {
  echo('Error en la conexión.');
  exit();
}

$resultado = $videoteca->query(
  'SELECT * FROM genero'
);

if ($resultado == FALSE) {
  echo('Error en la consulta.');
}

$generos = array();

while ($fila = $resultado->fetch_assoc()) {

  array_push($generos,$fila);

}

$resultado->close();
$videoteca->close();

$smarty = new Smarty;
$smarty->assign('generos',$generos);
$smarty->display($plantilla);

?>
~~~~

Enlaces interesantes
--------------------

El más interesante de los enlaces, al menos en lo que concierne a este capítulo, es la página Web de [Smarty](http://smarty.net/).
