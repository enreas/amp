Página HTML
-----------

Vamos a comenzar viendo un ejemplo de página HTML. Su código es el siguiente:

~~~~ {.html}
<html>
  <head>
    <title>Prueba</title>
  </head>
  <body>
    <h1>Esto es una página HTML.</h1>
  </body>
</html>
~~~~

En lo que a nosotros respecta, estas páginas son *estáticas*: su contenido es inalterable.

La hora en PHP
--------------

Nuestra primera página PHP sólo muestra la hora. Su código es muy sencillo:

~~~~ {.php}
La hora es: <?= date("H:i:s") ?>.
~~~~

Mientras que la página HTML que vimos anteriormente era estática (siempre que la veamos con un cliente Web su aspecto será el mismo), el de esta página PHP siempre será diferente, dependiendo de la hora en la que la página sea mostrada. Es por ello que estas páginas se conocen como *dinámicas*.

Funciones de salida
-------------------

PHP nos proporciona diferentes formas de mostrar información en las páginas. Hemos visto ésta:

~~~~ {.php}
La hora es: <?= date("H:i:s") ?>.
~~~~

Pero también podemos utilizar las funciones **echo** y **print**:

~~~~ {.php}
La hora es: <? echo date("H:i:s") ?><br/>.
La hora es: <? print date("H:i:s") ?><br/>.
~~~~

Comentarios
-----------

Es recomendable que introduzcamos comentarios en nuestras páginas PHP. Estos comentarios serán ignorados por el preprocesador de PHP, pero aquellos que tengan acceso al código fuente de las páginas podrán leerlos. Por lo general, los comentarios se utilizan para *comentar* aspectos del programa, indicar cuándo fue creado, por quién o qué tareas quedan por realizar. Existen dos tipos de comentario: de línea y de párrafo. Si comenzamos una línea con la pareja de caracteres **//**, esa línea será tomada por PHP como un comentario:

~~~~ {.php}
<?php

// El siguiente programa muestra la hora actual.
//
// La función date() recibe como parámetro el
// formato en el que se mostrará. La hora se
// representa como H y los segundos como s, pero
// atención que para los minutos se utiliza i y
// no m.
//
// Las horas, los minutos y los segundos se
// separarán con dos puntos.

?>

La hora es: <?= date("H:i:s") ?>.
~~~~

Los comentarios de párrafo comienzan con la pareja de caracteres **\*** y terminan con **\*/**:

~~~~ {.php}
<?php

/*
El siguiente programa muestra la hora actual.

La función date() recibe como parámetro el
formato en el que se mostrará. La hora se
representa como H y los segundos como s, pero
atención que para los minutos se utiliza i y
no m.

Las horas, los minutos y los segundos se
separarán con dos puntos.

*/

?>

La hora es: <?= date("H:i:s") ?>.
~~~~

El siguiente es un ejemplo de cómo utilizar los comentarios para incluir información en el encabezado de nuestras páginas:

~~~~ {.php}
<?php

/*

Autor:

  Juan Diego Gutiérrez Gallardo

Fecha:

  miércoles 24 de junio de 2009

Descripción:

  El siguiente programa muestra la hora
  actual.

  Se trata de un ejemplo de página
  dinámica muy sencillo.

  La función date() recibe como parámetro el
  formato en el que se mostrará. La hora se
  representa como H y los segundos como s,
  pero atención que para los minutos se
  utiliza i y no m.

  Las horas, los minutos y los segundos se
  separarán con dos puntos.

*/

?>

La hora es: <?= date("H:i:s") ?>.
~~~~

Variables
---------

Para declarar una variable en PHP sólo tenemos que escribir el nombre de la variable y asignarle un valor. Por ejemplo:

~~~~ {.php}
<?php
$hora = date("H:i:s");
?>
~~~~

El código anterior almacena en la variable **$hora** la hora tal y como la mostrábamos en los ejemplos anteriores. Las siguientes líneas son ejemplos de declaraciones correctas:

~~~~ {.php}
$hora = date("H:i:s");
$Hora = date("H:i:s");
$_hora = date("H:i:s");
$_1hora = date("H:i:s");
~~~~

Mientras que esta declaración es incorrecta, ya que su nombre comienza con un número:

~~~~ {.php}
$1hora = date("H:i:s");
~~~~

Tipos de datos: booleano
------------------------

Una variable de tipo booleano puede tener dos posibles valores: verdadero o falso. En el siguiente programa declaramos dos variables de tipo booleano:

~~~~ {.php}
<?php
$millonario = false;
$hipotecado = true;
?>
~~~~

Tipos de datos: entero
----------------------

En una variable de tipo entero podemos almacenar valores enteros positivos y negativos, incluido el cero. Por ejemplo:

~~~~ {.php}
<?php
$i = 20;
$j = -40;
$k = 0;
?>
~~~~

Tipos de datos: decimal
-----------------------

Una variable de tipo decimal puede contener cualquier número en coma flotante. Por ejemplo:

~~~~ {.php}
<?php
$pi = 3.14;
?>
~~~~

Tipos de datos: cadena
----------------------

Una cadena de texto no más que una secuencia de caracteres. PHP nos permite definir cadenas de tres formas diferentes:

1.  Con comillas simples.
2.  Con dobles comillas.
3.  En bloque.

Las cadenas más sencillas son las que se especifican entre comillas simples:

~~~~ {.php}
<?php
$cadena = 'Ésta es una simple cadena.';
?>
~~~~

Las cadenas entre comillas dobles tienen una característica añadida que las hace muy interesantes: si contienen el nombre de una variable existente, PHP sustituirá la variable por su valor. Por ejemplo:

~~~~ {.php}
<?php
$i = 12;
$cadena = "Un año tiene $i meses.";
?>
~~~~

Tras ejecutar estas dos líneas, el contenido de la variable **$cadena** será "Un año tiene 12 meses.".
El tercer tipo de cadenas, en bloque, es muy similar al segundo tipo, aunque son más adecuadas para cadenas de gran longitud:

~~~~ {.php}
<?php
$i = 12;
$cadena = <<<CADENA
Ésta es una cadena
definida en bloque.
Podemos utilizar variables como
en las cadenas de dobles comillas.
Esto es un ejemplo: $i.
CADENA;
?>
~~~~

Tipos de datos: matrices
------------------------

Las matrices nos permiten tener la información mucho más organizada, pudiendo acceder a una serie de valores a través de una sola variable, y utilizando un índice como modificador. La siguiente es una de las posibles formas de declarar una matriz que contenga tres valores:

~~~~ {.php}
<?php
$peliculas = array(9, 12, 21);
?>
~~~~

Para acceder a estos elementos sólo necesitamos indicar el índice de cada elemento tras el nombre de la variable, entre corchetes. Si los valores anteriores representasen las películas vistas en cada mes, podríamos mostrar esa información así:

~~~~ {.php}
<?php
$peliculas = array(9, 12, 21);
echo <<<PELICULAS
Películas vistas:<br/>
- en enero: $peliculas[0].<br/>
- en febrero: $peliculas[1]. <br/>
- en marzo: $peliculas[2]. <br/>
PELICULAS;
?>
~~~~

Podemos añadir nuevos elementos a una matriz después de haberla creado:

~~~~ {.php}
<?php
$peliculas = array(9, 12, 21);
$peliculas[] = 17;
?>
~~~~

PHP se encargará automáticamente de crear el cuarto elemento de la matriz. Para ver el contenido de la matriz tras añadir este nuevo elemento podemos utilizar una función muy útil: **print\_r()**, que recibe como argumento una matriz:

~~~~ {.php}
<?php
$peliculas = array(9, 12, 21);
$peliculas[] = 17;

print_r($peliculas);
?>
~~~~

En realidad, tras las matrices en PHP hay mucho más que lo que parece a primera vista. Más que matrices, podríamos considerar que PHP nos proporciona diccionarios, con los que podemos traducir de un valor a otro. La declaración de una matriz que hemos utilizado anteriormente es la versión simplificada de la misma, en la que sólo se asignan valores. Por ejemplo, ésta sería una forma igualmente válida de declarar la misma matriz, pero asociando palabras en lugar de números a cada valor:

~~~~ {.php}
<?php
$peliculas = array(
  'enero' => 9,
  'febrero' => 12,
  'marzo' => 21,
  'abril' => 17
);
print_r($peliculas);
?>
~~~~

Operador básico de asignación
-----------------------------

Ya hemos trabajado con el operador de asignación, el signo igual, en expresiones como:

~~~~ {.php}
<?php
$hora = date("H:i:s");
?>
~~~~

En dicha expresión, lo que está a la derecha del operador de asignación, el signo igual, se guarda en lo que esta a la izquierda.

Operadores aritméticos
----------------------

Son los que aprendimos a utilizar en la escuela, para sumar, restar, multiplicar y dividir:

-   suma: +.
-   resta: -.
-   multiplicación: \*.
-   división: /.

Combinando estos operadores con el operador de asignación podemos comenzar a realizar tareas verdaderamente útiles, como la suma de dos números:

~~~~ {.php}
<?php
$i = 2 + 3;
echo $i;
?>
~~~~

También podemos guardar esos valores en variables y posteriormente realizar la suma:

~~~~ {.php}
<?php
$i = 2;
$j = 3;
$i = $i + $j;
echo $i;
?>
~~~~

Existen unos operadores que combinan las asignaciones con los operadores aritméticos. Utilizando uno de estos operadores podemos volver a escribir el fragmento de código anterior de esta forma:

~~~~ {.php}
<?php
$i = 2;
$j = 3;
$i += $j; // Equivale a $i = $i + $j.
?>
~~~~

Lo mismo puede aplicarse al resto de operadores aritméticos, con lo que disponemos de **-=**, **\*=** y **/=**. Podríamos considerarlos como operadores extendidos de asignación.

Operadores de comparación
-------------------------

Nos permiten comparar dos valores. Nos basaremos en estas comparaciones para tomar decisiones en nuestro código. El resultado de utilizar este operador es un valor booleano:

~~~~ {.php}
<?php
$i = 1;
$j = 2;

echo '$i == $j: ';echo $i == $j;echo '<br/>';
echo '$i != $j: ';echo $i != $j;echo '<br/>';
echo '$i <> $j: ';echo $i <> $j;echo '<br/>';
echo '$i < $j:  ';echo $i < $j; echo '<br/>';
echo '$i > $j:  ';echo $i > $j; echo '<br/>';
echo '$i <= $j: ';echo $i <= $j;echo '<br/>';
echo '$i >= $j: ';echo $i >= $j;echo '<br/>';
?>
~~~~

Sólo cuando el resultado de la comparación sea verdadero se mostrará un uno.

Operadores lógicos
------------------

Operan con valores booleanos, devolviendo TRUE o FALSE dependiendo de si se cumple una condición o no. Veremos cuatro operadores lógicos: **and**, **or**, **xor** y **not**. El resultado de utilizar **and** sobre dos valores sólo es TRUE si los dos valores sobre los que se está realizando la operación son TRUE:

~~~~ {.php}
<?php
echo false and false;
echo false and true;
echo true and false;
echo true and true;
?>
~~~~

A diferencia del operador and, el resultado de utilizar **or** sobre dos valores sólo es FALSE si los dos valores sobre los que se está realizando la operación son FALSE:

~~~~ {.php}
<?php
echo false or false;
echo false or true;
echo true or false;
echo true or true;
?>
~~~~

El operador **xor** devuelve TRUE cuando los dos valores son distintos:

~~~~ {.php}
<?php
echo false xor false;
echo false xor true;
echo true xor false;
echo true xor true;
?>
~~~~

El operador **not (!)** es un operador unario, puesto que se aplica a una sola variable:

~~~~ {.php}
<?php
echo ! false;
echo ! true;
?>
~~~~

Operadores de incremento
------------------------

Y también de decremento. Gracias a estos operadores podemos simplificar operaciones como éstas:

~~~~ {.php}
<?php
$i = 0;
$i = $i + 1;
echo $i;
$i = $i - 1;
echo $i;
?>
~~~~

Que son equivalentes a las siguientes:

~~~~ {.php}
<?php
$i = 0;
$i++;
echo $i;
$i--;
echo $i;
?>
~~~~

Aunque ayudan a simplificar, en ocasiones pueden complicarlo todo. PHP nos deja situar estos operadores antes y después de la variable a la que modifican, pero dependiendo de dónde los coloquemos el resultado puede variar:

~~~~ {.php}
<?php
$i = 1;
$j = $i; // Tanto $i como $j valen 1.

echo $i++;
echo '<br/>';
echo ++$j;
?>
~~~~

Operadores de concatenación
---------------------------

La operación más importante que puede realizar sobre una cadena es la concatenación: unir dos cadenas para formar una sola. El operador de concatenación es el punto:

~~~~ {.php}
<?php
$cadena1 = 'Esto es';
$cadena2 = 'una cadena.';
$cadena3 = $cadena1 . ' ' . $cadena2;
echo $cadena3;
?>
~~~~

El resultado de esta operación es la cadena "Esto es una cadena.". Igual que en las operaciones aritméticas, podemos combinar una concatenación con una asignación para simplificar el código:

~~~~ {.php}
<?php
$cadena1 = 'Esto es';
$cadena2 = 'una cadena.';
$cadena1 .= ' ' . $cadena2;
echo $cadena1;
?>
~~~~

El resultado será el mismo en ambos casos.

Precedencia de operadores
-------------------------

Debemos estar siempre seguros del orden en el que PHP realizará las operaciones que le indiquemos. ¿Lo estamos ahora? ¿Cuál será el resultado de esta operación?

~~~~ {.php}
<?php
$i = 2 + 3 * 2;
echo $i;
?>
~~~~

¿Contendrá 10? ¿Contendrá 8? Depende de la precedencia que tengan los operadores. En este caso concreto, el resultado será 8, porque el operador de multiplicación tiene prioridad sobre el de suma. Podemos modificar la precedencia de los operadores utilizando paréntesis para envolver las operaciones que queremos realizar antes. La operación anterior puede ser modificada para obtener 10 como resultado:

~~~~ {.php}
<?php
$i = (2 + 3) * 2;
echo $i;
?>
~~~~

PHP realizará primero la operación que está entre paréntesis.

Cambios de tipo
---------------

Como resultado de las diferentes operaciones que realicemos sobre las variables, es posible que modifiquemos el tipo de alguna de ellas:

~~~~ {.php}
<?php
$i = 100; // Una variable de tipo entero.
$cadena = 'veces'; // Una cadena.
// Concatenamos el número y la cadena.
$i .= ' ' . $cadena;
echo $i;
?>
~~~~

¿Cuál será el resultado de estas operaciones? Vamos a ayudarnos de la función **gettype()**, que recibe como argumento el nombre de una variable y devuelve una cadena de texto que contiene el tipo de la variable pasada:

~~~~ {.php}
<?php
$i = 100; // Una variable de tipo entero.
$cadena = 'veces'; // Una cadena.
echo 'Tipo de $i: ' . gettype($i);
echo '<br/>';
// Concatenamos el número y la cadena.
$i .= ' ' . $cadena;
echo $i;
echo '<br/>';
echo 'Tipo de $i: ' . gettype($i);
?>
~~~~

Como puede comprobar, antes de la concatenación **$i** es de tipo entero (integer), pero después pasa a ser de tipo cadena (string). Para liar aún más las cosas, resulta que también podemos hacer esto otro:

~~~~ {.php}
<?php
$cadena = 'Soy una cadena';
echo gettype($cadena);
echo '<br/>';
$cadena = 20;
echo gettype($cadena);
?>
~~~~

Y hemos cambiado el tipo de una variable que era una cadena a un entero. Otra de las características de PHP es forzar el tipo de las variables. Podemos obligar a que una variable de un tipo determinado se comporte como una de otro tipo con solo indicar entre paréntesis y antes de la variable el tipo que deseamos:

~~~~ {.php}
<?php
$cadena = '10';
$entero = (integer) $cadena;
echo gettype($cadena);
echo '<br/>';
echo gettype($entero);
?>
~~~~

Tenga precaución, sin embargo. En el ejemplo anterior **$entero** contendrá el valor entero 10, pero si **$cadena** hubiese contenido la cadena "texto", el resultado sería que **$entero** contendría el valor 0:

~~~~ {.php}
<?php

$cadena = 'texto';
$entero = (integer) $cadena;

echo $entero;

?>
~~~~

Instrucciones de control condicionales
--------------------------------------

El siguiente es un ejemplo de utilización de la instrucción condicional **if**:

~~~~ {.php}
<?php

$hora = date('H');
if ($hora < '12')
  echo 'Aún no es mediodía.';

?>
~~~~

Podemos mejorar este pequeño programa utilizando **else** junto con if. Así podemos indicar la acción a seguir en caso de que la condición no se cumpla:

~~~~ {.php}
<?php

$hora = date('H');
if ($hora < '12')
  echo 'Aún no es mediodía.';
else
  echo 'Ya es mediodía.';

?>
~~~~

Lo que puede ampliarse utilizando **elseif**:

~~~~ {.php}
<?php

$hora = date('H');
if ($hora < '12')
  echo 'Aún no es mediodía.';
elseif ($hora == '12')
  echo 'Ya es mediodía.';
else
  echo 'El mediodía ya pasó';

?>
~~~~

Las instrucciones condicionales pueden anidarse, es decir, pueden escribirse unas dentro de otras, gracias a lo que podemos volver a escribir el código anterior así:

~~~~ {.php}
<?php

$hora = date('H');
if ($hora < '12')
  echo 'Aún no es mediodía.';
else
  if ($hora == '12')
    echo 'Ya es mediodía.';
  else
    echo 'El mediodía ya pasó';

?>
~~~~

Las llaves (**{}**) nos permiten asociar grupos de líneas a cada parte de la condición:

~~~~ {.php}
<?php

$hora = date('H');
if ($hora < '12'){
  echo 'Aún no es mediodía.<br/>';
}else{
  echo 'Es mediodía.<br/>';
  echo '¡Queda menos para ir a comer!<br/>';
}

?>
~~~~

Utilizando instrucciones if y else podemos complicar el programa anterior todo lo que queramos, por ejemplo comprobando la hora y escribiendo mensajes en función de la misma. Por ejemplo, para informar de que a las 9 de la mañana entramos a trabajar, a las 14 salimos a comer, a las 16 volvemos a entrar y a las 19 nos vamos para casa escribiríamos el siguiente código:

~~~~ {.php}
<?php

$hora = date('H');
if ($hora == '9'){
  echo 'Bienvenido al curro.';
}elseif ($hora == '14'){
  echo 'Que aproveche.';
}elseif ($hora == '16'){
  echo 'Lástima de siesta...';
}elseif ($hora == '19'){
  echo 'Hasta mañana.';
}

?>
~~~~

PHP nos proporciona otra instrucción condicional que permite escribir un programa equivalente al anterior, pero de forma más clara: la instrucción **switch**. Incluso podemos indicar qué hacer en caso de que no se cumpla ninguna de las condiciones:

~~~~ {.php}
<?php

$hora = date('H');
switch ($hora){
  case '9':
    echo 'Bienvenido al curro.';
    break;
  case '14':
    echo 'Que aproveche.';
    break;
  case '16':
    echo 'Lástima de siesta...';
    break;
  case '19':
    echo 'Hasta mañana.';
    break;
  default:
    echo 'A trabajar.';
}

?>
~~~~

Instrucciones de control iterativas: for
----------------------------------------

La instrucción for permite ejecutar un bloque de instrucciones un número concreto de veces. El siguiente fragmento de código contiene un bucle for que se ejecuta tres veces. Cada vez que lo hace, escribe el mismo texto:

~~~~ {.php}
<?php

for ($i = 1; $i <= 3; $i++){
  echo 'Probando un bucle for...<br/>';
}

?>
~~~~

El siguiente es un ejemplo mucho más vistoso de bucle for:

~~~~ {.php}
<?php

for ($i = 1; $i <= 3; $i++){
  echo '<h'.$i.'>Bucle for</h'.$i.'>';
}

?>
~~~~

Un bucle for puede utilizarse para recorrer todos los elementos de una matriz y mostrar su contenido. En este caso nos ayudaremos de las funciones <u>current</u> y <u>next</u>. La primera nos permite obtener el valor del elemento actual de la matriz, la segunda avanza una posición dentro de la matriz:

~~~~ {.php}
for ($i = 1; $i <= $meses; $i++){
  echo $i.': '.current($peliculas).'<br/>';
  next($peliculas);
}
~~~~

Instrucciones de control iterativas: foreach
--------------------------------------------

Traducido como "para cada", con foreach podemos recorrer todos los elementos de una matriz sin necesidad de saber cuántos elementos la componen. Esto nos puede ser de mucha utilidad en nuestro ejemplo de las películas por mes:

~~~~ {.php}
<?php

$peliculas = array(
  'enero' => 9,
  'febrero' => 12,
  'marzo' => 21,
  'abril' => 17
);

foreach ($peliculas as $mes => $vistas){
  echo 'En '.$mes.' vi '.$vistas.'.<br/>';
}

?>
~~~~

Instrucciones de control iterativas: while
------------------------------------------

Podemos crear un bucle de este tipo que retenga el control mientras que, dentro de la hora, el segundo en el que nos encontremos no sea cero:

~~~~ {.php}
<?php

$segundo = date('s');

while($segundo != '0'){
  $segundo = date('s');
}

echo 'Comienza un nuevo minuto.';

?>
~~~~

Existe otra variante de este bucle, que sigue las mismas premisas que el anterior, pero con algunas salvedades que comprenderemos mejor con un ejemplo:

~~~~ {.php}
<?php

do{
  $segundo = date('s');
}while($segundo != '0');

echo 'Comienza un nuevo minuto.';

?>
~~~~

Debemos utilizar **do while** cuando queramos que el cuerpo del bucle se ejecute al menos una vez, y **while** en caso contrario.

Es posible que los ejemplos anteriores provoquen un error si el tiempo máximo de ejecución definido en php.ini es inferior a 60 segundos. Puede modificar ese valor a través de la variable <u>max\_execution\_time</u>.

Salida de bucles
----------------

Es posible alterar el flujo de la ejecución de un bucle mediante algunas instrucciones especiales: **break** y **continue**. La primera de ellas, **break**, puede utilizarse para detener la ejecución de los bucles for, foreach, while y do while:

~~~~ {.php}
<?php

do{
  $segundo = date('s');
  if ($segundo <= '10'){
    break;
  }
}while($segundo != '0');

echo 'Comienza un nuevo minuto.';

?>
~~~~

Por otra parte, podemos utilizar **continue** cuando queramos saltar el resto de líneas de código asociadas a un bucle y continuar con la siguiente iteración:

~~~~ {.php}
<?php

$peliculas = array(
  'enero' => 9,
  'febrero' => 12,
  'marzo' => 0,
  'abril' => 17
);

foreach ($peliculas as $mes => $vistas){
  if ($vistas == 0){
    continue;
  }
  echo 'En '.$mes.' vi '.$vistas.'.<br/>';
}

?>
~~~~

Como resultado, el código anterior sólo mostrará in-formación sobre los meses de enero, febrero y abril.

Enlaces interesantes
--------------------

Puede que encuentre interesantes los siguientes enlaces:

-   la [documentación](http://www.php.net/manual/) de PHP.
-   también [en español](http://www.php.net/manual/es/).
-   todos [los documentos](http://www.php.net/docs.php) de PHP.
-   el editor de PHP que recomendamos, [eclipse.org/pdt/ PDT](http://).
-   otros editores:
    -   [Zend Studio](http://www.zend.com/products/studio/).
    -   [NetBeans](http://www.netbeans.org/features/php/).
    -   [Aptana PHP](http://www.aptana.com/php).
    -   [Codelobster](http://www.codelobster.com/).
    -   [Nusphere phpED](http://www.nusphere.com/).

-   para crear [aplicaciones de escritorio con PHP](http://gtk.php.net/).
-   el buscador de acrónimos [Acronym Finder](http://www.acronymfinder.com/).
