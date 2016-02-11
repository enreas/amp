Funciones y parámetros
----------------------

Podemos crear nuestras propias funciones. En primer lugar, hemos de declarar la lista de parámetros que recibirá la función. Éste es un sencillo ejemplo:

~~~~ {.php}
function par_impar($numero){

  if($numero % 2 == 0){
    print('par');
  }else{
    print('impar');
  }

}
~~~~

Se trata de una función que, dado un número, escribr una cadena de texto diciendo si el número es par o impar. Podemos utilizarla asi:

~~~~ {.php}
<?php

function par_impar($numero){

  if($numero % 2 == 0){
    printf('par');
  }else{
    print('impar');
  }

}

par_impar(7);

?>
~~~~

Los parámetros pueden pasarse por *valor* y por *referencia*. La primera es la opción predeterminada: la función recibe una copia del parámetro pasado, así que aunque sea modificado dentro de la función dicho cambio sólo tendrá efecto dentro del cuerpo de la función. Por el contrario, si queremos que los parámetros que pasemos a la función puedan ser modificados, y que esos cambios sobrevivan a la llamada de la función, hemos de indicarle a PHP que ese parámetro se va a pasar por referencia. El siguiente es un ejemplo de función en la que los parámetros se pasan por valor:

~~~~ {.php}
<?php

function incrementar($numero){
  $numero++;
}

$i = 7;
incrementar($i);
print($i);

?>
~~~~

Como puede comprobar, el incremento no afecta al valor de la variable pasada a la función. Pero esto cambia si el parámetro se pasa por referencia:

~~~~ {.php}
<?php

function incrementar(&$numero){
  $numero++;
}

$i = 7;
incrementar($i);
print($i);

?>
~~~~

Es posible asociar valores predeterminados a los parámetros para los casos en los que no se pase ningún valor a la función:

~~~~ {.php}
<?php

function par_impar($numero){

  if($numero % 2 == 0){
    printf('par');
  }else{
    print('impar');
  }

}

par_impar();

?>
~~~~

Si intenta cargar la página anterior, PHP le avisará de que no se ha pasado argumento alguno a la función. Pero esto puede evitarse con sólo modificar la función:

~~~~ {.php}
<?php

function par_impar($numero = 0){

  if($numero % 2 == 0){
    printf('par');
  }else{
    print('impar');
  }

}

par_impar();

?>
~~~~

También es posible crear funciones con un número de parámetros variable. Para ello, hemos de crear una función sin parámetro alguno y luego ayudarnos de las funciones de PHP **func\_num\_args()**, **func\_get\_arg()** y **func\_get\_args()**. Gracias al uso de estas funciones podemos crear con mucha facilidad una función que recibe una lista de valores y muestra en la página el resultado de sumarlos todos:

~~~~ {.php}
<?php

function sumatorio(){

  $parametros = func_num_args();

  $res = 0;
  for($i = 0; $i < $parametros; $i++){
    $res += func_get_arg($i);
  }

  printf($res);

}

sumatorio(3, 14, 15);

?>
~~~~

Utilizando la función **func\_get\_args()**, que devuelve una matriz con todos los argumentos, y el bucle foreach podemos simplificar bastante el código:

~~~~ {.php}
<?php

function sumatorio(){

  $res = 0;
  foreach(func_get_args() as $param){
    $res += $param;
  }

  printf($res);
}

sumatorio(3, 14, 15);

?>
~~~~

Devolución de valores en funciones
----------------------------------

Nada más fácil que conseguir que una función escrita por nosotros devuelva un valor. Sólo hemos de utilizar la instrucción **return**. Por ejemplo, podemos cambiar el código de la función par\_impar() que construimos anteriormente. Ahora la función devolverá TRUE si el número pasado es par, y FALSE en caso contrario:

~~~~ {.php}
function par_impar($numero){

  if($numero % 2 == 0){
    return TRUE;
  }else{
    return FALSE;
  }

}
~~~~

El siguiente sería un programa que utilizaría esa función:

~~~~ {.php}
<?php

function par_impar($numero){

  if($numero % 2 == 0){
    return TRUE;
  }else{
    return FALSE;
  }

}

if(par_impar(7) == TRUE){
  print('par');
}else{
  print('impar');
}

?>
~~~~

Trabajando con funciones
------------------------

Ejecute el siguiente código SQL para crear la base de datos videoteca y la tabla genero e insertar los valores iniciales:

~~~~ {.sql}
DROP DATABASE IF EXISTS videoteca;
CREATE DATABASE videoteca;

USE videoteca;

CREATE TABLE genero (
  id INTEGER UNSIGNED NOT NULL AUTO_INCREMENT,
  nombre VARCHAR(2) NOT NULL,
  descripcion VARCHAR(32) NOT NULL,
  PRIMARY KEY(id)
);

INSERT INTO genero(nombre,descripcion)
VALUES('CF', 'Ciencia Ficción');

INSERT INTO genero(nombre,descripcion)
VALUES('A', 'Aventuras');

INSERT INTO genero(nombre,descripcion)
VALUES('D', 'Drama');

INSERT INTO genero(nombre,descripcion)
VALUES('T', 'Terror');
~~~~

La siguiente página PHP cargará el contenido de la tabla que acabamos de crear y mostrará su contenido:

~~~~ {.php}
<?php

$conexion = mysqli_connect(
  'localhost',
  'root',
  '',
  'videoteca'
);

if ($conexion == FALSE){
  echo('Error en la conexión.');
  exit();
}

$res = mysqli_query(
  $conexion,
  'SELECT * FROM genero'
);

if ($res == FALSE){
  echo('Error en la consulta.');
  mysqli_close($conexion);
  exit();
}

?>

<table border="1">
  <tr>
    <th>id</th>
    <th>nombre</th>
    <th>descripcion</th>
  </tr>

<?php

while($fila = mysqli_fetch_row($res)){
  printf('<tr>');
  printf(
    "<td>%u</td><td>%s</td><td>%s</td>",
    $fila[0], $fila[1], $fila[2]
  );
  printf('</tr>');
}

?>

</table>

<?php

mysqli_free_result($res);
mysqli_close($conexion);

?>
~~~~

Si se fija en el listado de la página podrá comprobar que el código PHP está muy mezclado con el código HTML. Ha sido necesario escapar de PHP en varias ocasiones. ¿No sería mucho más cómodo disponer de todo el código que se encarga de obtener el listado de géneros agrupado? Un primer intento podría ser algo así:

~~~~ {.php}
<?php

$conexion = mysqli_connect(
  'localhost',
  'root',
  '',
  'videoteca'
);

if ($conexion == FALSE){
  echo('Error en la conexión.');
  exit();
}

$res = mysqli_query(
  $conexion,
  'SELECT * FROM genero'
);

if ($res == FALSE){
  echo('Error en la consulta.');
  mysqli_close($conexion);
  exit();
}

while($fila = mysqli_fetch_row($res)){
  $generos .= '<tr>';
  $generos .= sprintf(
    "<td>%u</td><td>%s</td><td>%s</td>",
    $fila[0], $fila[1], $fila[2]
  );
  $generos .= sprintf('</tr>');
}

mysqli_free_result($res);
mysqli_close($conexion);

?>

<table border="1">
  <tr>
    <th>id</th>
    <th>nombre</th>
    <th>descripcion</th>
  </tr>

<?php

  print($generos);

?>

</table>
~~~~

El resultado es el mismo que se obtuvo en el ejemplo anterior, aunque el código está algo más ordenado, con dos bloques: uno con PHP y otro con HTML. ¿Qué nos impide ubicar todo el código PHP en una única función que devuelva como resultado una cadena de texto con todos los géneros cinematográficos, en filas para insertar en una tabla? Por ejemplo, así:

Inclusión de archivos
---------------------

Vamos a crear un nuevo archivo PHP cuyo contenido será única y exclusivamente la función de carga de géneros que creamos en la sección anterior. Ese archivo podría llamarse generos.php:

~~~~ {.php}
<?php

function cargar_generos(){

  $conexion = mysqli_connect(
    'localhost',
    'root',
    '',
    'videoteca'
  );

  if ($conexion == FALSE){
    echo('Error en la conexión.');
    exit();
  }

  $res = mysqli_query(
    $conexion,
    'SELECT * FROM genero'
  );

  if ($res == FALSE){
    echo('Error en la consulta.');
    mysqli_close($conexion);
    exit();
  }

  while($fila=mysqli_fetch_row($res)){
    $generos .= '<tr>';
    $generos .= sprintf(
      "<td>%u</td><td>%s</td><td>%s</td>",
      $fila[0], $fila[1], $fila[2]
    );
    $generos .= sprintf('</tr>');
  }

  mysqli_free_result($res);
  mysqli_close($conexion);

  return $generos;
}

?>
~~~~

A continuación, crearemos un segundo archivo PHP con el siguiente contenido:

~~~~ {.php}
<?php

include 'generos.php';

?>

<table border="1">
  <tr>
    <th>id</th>
    <th>nombre</th>
    <th>descripcion</th>
  </tr>

 <?php

  print(cargar_generos());

?>

</table>
~~~~

Cuando PHP analiza el archivo para saber qué tiene que hacer cuando alguien le pide esa página, encuentra la instrucción include. Entonces busca el archivo indicado a continuación y lo incluye en la página actual, como si efectivamente estuviese escrito en la página.

Orientación a objetos
---------------------

Podemos definir una clase como la plantilla utilizada para crear un objeto. En una clase se definen las propiedades y los métodos de los objetos creados a partir de ella. Vamos a crear una que llamaremos generos.cls.php y que realizará la operación de carga de todos los generos. Su código es el siguiente:

~~~~ {.php}
<?php

class clsGeneros{

  var $Ordenada = False;

  function Cargar(){

    $conexion = mysqli_connect(
      'localhost',
      'root',
      '',
      'videoteca'
    );

    if ($conexion == FALSE){
      echo('Error en la conexión.');
      exit();
    }

    if($this->Ordenada == TRUE){
      $res = mysqli_query(
        $conexion,
        'SELECT * FROM genero '.
        'ORDER BY nombre'
      );
    }else{
      $res = mysqli_query(
        $conexion,
        'SELECT * FROM genero'
      );
    }

    if ($res == FALSE){
      echo('Error en la consulta.');
      mysqli_close($conexion);
      exit();
    }

    while($fila=mysqli_fetch_row($res)){
      $generos .= '<tr>';
      $generos .= sprintf(
        "<td>%u</td><td>%s</td><td>%s</td>",
        $fila[0], $fila[1], $fila[2]
      );
      $generos .= sprintf('</tr>');
    }

    mysqli_free_result($res);
    mysqli_close($conexion);

    return $generos;
  }

}

?>
~~~~

Este archivo será incluido en aquellos programas que quieran utilizar la clase. Por ejemplo, en éste:

~~~~ {.php}
<?php

require 'generos.cls.php';

$oGeneros = new clsGeneros;
$oGeneros->Ordenada = TRUE;
$sGeneros = $oGeneros->Cargar();

?>

<table border="1">
  <tr>
    <th>id</th>
    <th>nombre</th>
    <th>descripción</th>
  </tr>

<?php

  print($sGeneros);

?>

</table>
~~~~

Usando la clase que hemos creado, el siguiente programa es perfectamente válido:

~~~~ {.php}
<?php

require 'generos.cls.php';

$oGeneros1 = new clsGeneros;
$oGeneros2 = new clsGeneros;

$oGeneros1->Ordenada = TRUE;
$oGeneros2->Ordenada = FALSE;

$sGeneros1 = $oGeneros1->Cargar();
$sGeneros2 = $oGeneros2->Cargar();

?>
<table><tr><td>
<table border="1">
  <tr>
    <th>id</th>
    <th>nombre</th>
    <th>descripción</th>
  </tr>

 <?php

  print($sGeneros1);

?>

</table>
</td><td>
<table border="1">
  <tr>
    <th>id</th>
    <th>nombre</th>
    <th>descripción</th>
  </tr>

 <?php

  print($sGeneros2);

?>

</table>
</td></tr></table>
~~~~

Constructores
-------------

Dentro de una clase, el constructor es un método especial que se ejecuta siempre que se crea un ejemplar de dicha clase. El nombre de ese método especial debe ser el mismo que el de la clase:

~~~~ {.php}
var $Ordenada;
...
function clsGeneros(){
  $this->Ordenada = TRUE;
}
~~~~

Hemos movido la asignación de un valor inicial a **\$Ordenada** de la declaración al constructor. Ahora, el comportamiento predeterminado del método de carga será devolver la lista de géneros ordenada. Como si de un método normal y corriente se tratase, podemos definir una lista de argumentos que el constructor puede recibir. Sería interesante que recibiese si que-remos o no la lista de géneros ordenada:

~~~~ {.php}
function clsGeneros($fOrdenada){
  $this->Ordenada = $fOrdenada;
}
~~~~

Al crear el ejemplar de la clase indicaremos si queremos ordenación o no:

~~~~ {.php}
$oGeneros = new clsGeneros(TRUE);
~~~~
