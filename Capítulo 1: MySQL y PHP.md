==XAMPP==
* La página principal de XAMPP se encuentra [http://apachefriends.org/ aquí].
* Puedes aprender más sobre XAMPP en la página que le dedica la [http://en.wikipedia.org/wiki/XAMPP Wikipedia].
* Comparación de [http://en.wikipedia.org/wiki/Comparison_of_WAMPs WAM]
* Comparación de [http://en.wikipedia.org/wiki/LAMP_(software_bundle) LAMPs].
* Comparación de [http://en.wikipedia.org/wiki/List_of_AMP_Packages AMPs].

==Otras herramientas==
Aunque durante el resto del libro se repetirán algunos de los enlaces que veremos a continuación, los incluimos también aquí para los más impacientes:
* las [http://dev.mysql.com/downloads/gui-tools/ herramientas de MySQL].
* la página de [http://dev.mysql.com/workbench/ MySQL Workbench].
* [http://www.phpmyadmin.net/ phpMyAdmin].

==Creación de la base de datos==
Aunque durante este capítulo habrá aprendido a crear sus propias bases de datos utilizando las herramientas de MySQL, es posible realizar la misma operación utilizando el lenguaje SQL.
Esta forma de trabajo tiene algunas ventajas, como que es posible repetir la operación de creación de la base de datos tantas veces como sea necesario, tanto si se tiene acceso al centro de control como si no.
El código necesario para crear la base de datos para la videoteca es el siguiente:
<source lang="sql" >
DROP DATABASE IF EXISTS videoteca;
CREATE DATABASE videoteca;
</source>
Recuerde que el usuario con el que se haya conectado a MySQL debe tener permisos suficientes para crear bases de datos si quiere ejecutar el código anterior.

==Creación de la tabla==
Hemos creado la base de datos para organizar datos dentro de una determinada temática. Estos datos estarán agrupados en tablas. En concreto, en el ejemplo desarrollado en este capítulo sólo utilizaremos una tabla que puede crearse con el siguiente código SQL:
<source lang="sql">
USE videoteca;

CREATE  TABLE genero (
  id INT UNSIGNED NOT NULL AUTO_INCREMENT,
  nombre VARCHAR(2) NOT NULL,
  descripcion VARCHAR(32) NOT NULL,
  PRIMARY KEY (id)
);
</source>
La primera línea nos permite indicar con qué base de datos queremos trabajar. Las lineas siguientes permiten definir el nombre de la tabla a crear y los campos que contendrá.

==Inserciones==
De poco nos sirve disponer de una base de datos y sus tablas si no almacenamos nada en su interior. Las siguientes instrucciones SQL permiten insertar algunos registros en la tabla <u>genero</u> de la base de datos <u>videoteca</u>:
<source lang="sql" >
USE videoteca;

INSERT INTO genero(nombre, descripcion)
VALUES('CF', 'Ciencia Ficción');

INSERT INTO genero(nombre, descripcion)
VALUES('A', 'Aventuras');

INSERT INTO genero(nombre, descripcion)
VALUES('D', 'Drama');
</source>

==Lista de géneros usando PHP==
El siguiente código PHP permite conectar con la base de datos <u>videoteca</u> y seleccionar todos los registros de la tabla <u>genero</u>, mostrando cada registro en una línea diferente:
<source lang="php" >
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

$resultado = mysqli_query(
  $conexion,
  'SELECT * FROM genero'
);

if ($resultado == FALSE){
  echo('Error en la consulta.');
  mysqli_close($conexion);
  exit();
}

while($fila = mysqli_fetch_row($resultado)){
  printf(
    "(%u) %s - %s<br/>",
    $fila[0], $fila[1], $fila[2]
  );
}

mysqli_free_result($resultado);
mysqli_close($conexion);

?>
</source>

==Lista de géneros usando PHP, en una tabla==
En el ejemplo anterior cada género se mostraba en una línea diferente. Podemos cambiar el aspecto con el que se presentan estos datos usando tablas HTML.
El siguiente es el código necesario para lograrlo. Fíjese en que sólo hemos sustituido el bucle <u>while</u> por otras líneas de código:
<source lang="php" >
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

$resultado = mysqli_query(
  $conexion,
  'SELECT * FROM genero'
);

if ($resultado == FALSE){
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

while($fila = mysqli_fetch_row($resultado)){
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

mysqli_free_result($resultado);
mysqli_close($conexion);

?>
</source>

==Una inserción más==
Realice la siguiente inserción en la tabla genero de la base de datos:
<source lang="sql" >
USE videoteca;

INSERT INTO genero(nombre, descripcion)
VALUES('T', 'Terror');
</source>
Tras esta inserción, las páginas PHP que muestran la lista de géneros incluirán un nuevo género, sin necesidad de cambiar el código fuente de dichas páginas.
