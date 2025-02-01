[Home](index.md)

--limit and order by
SELECT * FROM tabla WHERE campo > 10 ORDER BY campo2 LIMIT 5

--Transformando un string a mayúsculas/minusculas: no modifica datos en la tabla, solo los transforma para la consulta
select upper(email) as email_upper from usuarios;
select lower(email) as email_lower from usuarios;

--Quitando espacios en blanco de un string
select trim(nombre), trim(email) from usuarios;

--Combinando funciones
select lower(trim(nombre)) as nombre_limpio, lower(trim(apellido)) as apellido_limpio, lower(trim(email)) as email_limpio from usuarios;

--Obteniendo el largo de un string
select length(apellido) from usuarios;
select apellido, length(apellido) from usuarios;

--Obteniendo el nombre mas largo de la tabla
Por ejemplo, si queremos seleccionar el largo del nombre más corto de la tabla usuarios, la consulta quedaría así:

SELECT LENGTH(nombre) as largo_nombre FROM usuarios ORDER BY LENGTH(nombre) LIMIT 1 ;

Por otro lado, si queremos obtener el largo del nombre más largo, invertiremos el orden de la selección.

SELECT LENGTH(nombre) as largo_nombre FROM usuarios ORDER BY LENGTH(nombre) DESC LIMIT 1 ;

select length(email) from usuarios order by length(email) desc limit 3;

--Ordenando todos los datos y la funcion
se nos solicita el correo electrónico más largo en lugar de simplemente su longitud
SELECT email FROM usuarios ORDER BY LENGTH(email) DESC LIMIT 1;

Además, es posible que se nos solicite seleccionar todos los campos del usuario cuyo correo sea el más largo. Para lograrlo, podemos utilizar la siguiente consulta:
SELECT * FROM usuarios ORDER BY LENGTH(email) DESC LIMIT 1;

Asimismo, podrían requerir que seleccionemos todos los campos de la tabla y, además, incluir el largo del correo electrónico. La idea es similar, simplemente agregamos la función al SELECT:
SELECT *, LENGTH(email) as largo_email FROM usuarios ORDER BY LENGTH(email) DESC LIMIT 1;

select email, length(email) from usuarios order by length(email) desc limit 3;

--Concatenar strings
SELECT nombre || ' ' || apellido AS nombre_completo FROM empleados;

select producto || '-' || marca as marca_producto from productos;

--Seleccionando caracteres de un string con SUBSTR
SUBSTR( string, inicio, largo )
En la sintaxis podemos observar que la función tiene 3 argumentos. 1. String: el nombre de la columna o palabra que será utilizada 2. Inicio: un integer que especifica la posicion de inicio desde la cual se extraerán caracteres al string. 3. Largo: la cantidad de caracteres extraidos
Por ejemplo, si tenemos una tabla de productos con el campo 'nombre' y queremos seleccionar sólo la primera letra de cada nombre, podemos utilizar la siguiente consulta:
SELECT SUBSTR(nombre, 1, 1) AS primera_letra FROM productos;
para lograr este mismo objetivo en PostgreSQL, deberíamos usar la función LEFT()

select substr(apellido, 1, 3) as primeras_letras from usuarios;
select substr(apellido, 2, 3) as tres_caracteres_del_apellido from usuarios
where nombre like 'Mar%';

--Obteniendo la fecha de hoy
DATE() podemos obtener la fecha de hoy.
MySQL se utiliza CURDATE(), y en Microsoft SQL Server se utiliza GETDATE() A la hora de buscar documentación es importante dejar claro que motor se está ocupando. En este tutorial interactivo estamos ocupando SQLite.

SELECT * FROM usuarios WHERE fecha_registro = DATE(); -- both brings information the todays date
SELECT * FROM usuarios WHERE fecha_registro = DATE('now');

Select descripcion from tareas where fecha_limite = DATE();

--Obteniendo la fecha de mañana
EN SQL es posible sumar fechas para obtener fechas futuras. En SQLite lo podemos lograr pasando un segundo argumento a la función DATE
DATE('now', '1 day')
En este ejemplo, estamos sumando 1 día a la fecha de hoy (now). Si queremos sumar más días, por ejemplo 5 días, utilizaremos DATE('now', '5 day').
También es posible sumar semanas y meses con:
2 Semanas: DATE('now', '2 week') 3 Meses: DATE('now', '3 month')
En una consulta esto se vería de la siguiente forma:
SELECT * FROM tabla where fecha > DATE('now', '2 week')

Select * from tareas where fecha_limite = DATE('now','1 day');

--Obteniendo la fecha de ayer
Así como es posible sumar fechas, también es posible restarlas:
DATE('now', '-1 day') DATE('now', '-1 week')
Es importante aclarar que cuando no especificamos el signo, se asume que es positivo, esto quiere decir que
DATE('now', '1 day')
es lo mismo que
DATE('now', '+1 day')

Select monto from ganancias where fecha = DATE('now','-1 day');

--Extracción del año
Nos piden mostrar toda la información de la tabla y adicionalmente agregar una columna con el año de la venta.
SELECT *, strftime('%Y', fecha_venta) as año_venta FROM ventas
Para mostrar los resultados de este tipo de funciones, es necesario asignar un nombre a la nueva columna, ya que, de lo contrario, la columna resultante mantendrá el nombre de "strftime('%Y', fecha_venta)", lo cual resultaría en una denominación poco legible para un informe.

select monto, strftime('%Y',fecha_venta) as año_venta from ventas;

--Extracción del mes
SELECT strftime('%m', columna) FROM tabla
En este caso, para obtener el mes, pasamos %m como argumento a la función strftime.

Select monto, strftime('%m',fecha_venta) as mes_venta, strftime('%Y',fecha_venta) as año_venta from ventas;

--Extracción del mes y año
Ya aprendimos a extraer el mes y el año de una fecha. Sin embargo, cómo podriamos extraer ambos datos en una sola columna?
Para extraer tanto el mes como el año de una fecha en una sola columna, puedes utilizar la función strftime('%m-%Y'). Esto te permitirá obtener un resultado en el formato "mes-año".

select monto, strftime('%m-%Y',fecha_venta) as mes_año from ventas;

--Extracciones y where
Nos piden mostrar todas las ventas del año 2012. Para esto utilizaremos la función strftime para extraer el año de las fechas, y luego filtraremos por el año indicado:
SELECT * FROM ventas WHERE strftime('%Y', fecha_venta) = '2012';

Select * from ventas where strftime('%Y',fecha_venta) = '2015';

--Funciones de agregación
--El mayor valor de una columna
la función MAX() la cual nos permite encontrar el valor más alto del campo que especifiquemos.
SELECT MAX(columna) FROM tabla
Podemos encontrar el salario más alto utilizando:
SELECT MAX(salario) FROM empleados;
Cuando usamos funciones de agregación, no podemos seleccionar directamente otros elementos de la misma tabla. Por ejemplo, SELECT email, MAX(salario) FROM empleados; arrojaría error ya que estaríamos seleccionando email junto a la función. Pero no te preocupes, ya que aprenderemos cómo hacerlo apropiadamente cuando veamos la cláusula group by más adelante.

select MAX(edad) from empleados;

--El menor valor de una columna
. Esta función toma como argumento el nombre de la columna y devuelve el valor más pequeño en esa columna.
SELECT MIN(columna) FROM tabla

select MIN(sueldo) from empleados;

--Suma de elementos en una columna
SUM(). Con esta podemos sumar todos los elementos de una columna.
SELECT SUM(columna) FROM tabla
Es importante tener en cuenta que la columna sobre la cual se aplica la función SUM() debe contener valores numéricos, de lo contrario, la consulta puede generar un error o un resultado inesperado.

select sum(sueldo) from empleados;

--Promedio de una columna
AVG(). El nombre de la función viene del término en inglés average
SELECT AVG(columna) FROM tabla

select AVG(sueldo) from empleados;

--Contando elementos en una tabla
COUNT(). Con esta podemos contar la cantidad de registros dentro de una tabla.
SELECT COUNT(*) FROM tabla

select count(*) from empleados;

--Ejercicio 1 : Funciones de agregacion con where
SELECT AVG(columna1) FROM tabla WHERE columna2 < valor
select sum(sueldo) from empleados where edad > '27'

--Ejercicio 2 : Funciones de agregacion con where
select AVG(sueldo) from empleados where sueldo > '50000'

--Ejercicio 3
select count(*) from empleados where departamento = 'Marketing'

--Ejercicio 4 :
And es excluyente. cuando quieres filtrar por múltiples valores en una columna, debes usar OR en lugar de AND

select count(*) from empleados 
where departamento = 'Finanzas'
or departamento = 'Marketing';

--Conteo con condiciones con string
select count(*) from usuarios where nombre like'%a'

--Distinct
--Seleccionar filtrando datos repetidos
En SQL el keyword DISTINCT nos permite filtrar los resultados repetidos de una consulta.
SELECT DISTINCT color AS color_unico
FROM colores

select distinct color as color_unico from colores;

--Seleccionando correos únicos
select distinct correo as correo_unico from usuarios;

--Seleccionar distintos años
Se nos ha solicitado crear una consulta que muestre los años en los que se han realizado transacciones, excluyendo repeticiones.
Como ya aprendimos en ejercicios anteriores, para obtener el año a partir de la fecha de venta, podemos utilizar el siguiente código:
SELECT strftime('%Y', fecha_venta) as año_venta FROM ventas
Sin embargo, para asegurarnos de obtener años únicos, podemos agregar la cláusula DISTINCT a nuestra consulta de la siguiente manera:
SELECT DISTINCT strftime('%Y', fecha_venta) as año_unico FROM ventas

select distinct strftime('%m', fecha_venta) as mes_unico from ventas;

--Contar los valores distintos
Si queremos contar los valores distintos en una columna de una tabla, podemos combinar las funciones COUNT y DISTINCT de la siguiente manera:COUNT(DISTINCT columna)

select count(distinct telefono) as telefonos_unicos from usuarios;

--Contando correos únicos
select count(distinct correo) as correos_cant from usuarios;

--Distinct con múltiples columnas
Podemos usar DISTINCT con más de una columna para obtener combinaciones únicas de esas columnas. Supongamos que tienes una tabla llamada empleados con las columnas departamento y puesto.
Luego podemos obtener todas las combinaciones únicas de Departamento y Puesto utilizando la siguiente consulta:
SELECT DISTINCT departamento, puesto FROM empleados;

select distinct categoria, precio from productos;

--Introduccion a Grupos
--Agrupando valores con GROUP BY
La cláusula GROUP BY es una poderosa herramienta en SQL que se utiliza para agrupar filas con valores idénticos en una o varias columnas específicas, permitiendo realizar operaciones de agregación en cada grupo.
En este primer ejercicio aprenderemos a utilizar GROUP BY para obtener todos los elementos distintos de una tabla, lo mismo que previamente hicimos con distinct.

Podemos seleccionar los elementos únicos utilizando GROUP BY de la siguiente forma:
SELECT color as color_unico FROM colores GROUP BY color

select correo as correo_unico from usuarios group by correo;

--Agrupar y contar
GROUP BY es comúnmente utilizada junto con funciones de agregación como COUNT, MAX, MIN, SUM y AVG para obtener información resumida de un conjunto de datos.
Queremos saber cuantas veces aparece cada color. Esto lo podemos lograr combinando GROUP BY y la función de agregación COUNT
SELECT color, COUNT(color) as Repeticiones FROM colores GROUP BY color

Select correo, count(correo) as repeticiones from usuarios group by correo;

--Ejercitando agrupar y contar
Select departamento, count(departamento) as cantidad_empleados from empleados group by departamento;

--Agrupar y sumar
Si queremos calcular cuanto ha gastado cada cliente, podemos realizar la siguiente consulta
SELECT Cliente, SUM(Monto) AS Monto_Total FROM pedidos GROUP BY Cliente;

Select categoria, SUM(monto) as monto_total from ventas group by categoria;

--Agrupar y promediar
calcular promedios por grupo.
SELECT grupo, AVG(columna) FROM tabla GROUP by grupo

Select nombre_completo, AVG(nota) as Promedio_Notas from estudiantes group by nombre_completo;

--Máximo por grupo
obtener el monto mas alto de cada grupo. La sintaxis de la consulta será igual a las vistas previamente, es decir:
SELECT grupo, MAX(columna) FROM tabla GROUP by grupo

select categoria, MAX(monto) as monto_mas_alto from ventas group by categoria;

--Mínimo por grupo
obtener el monto mas bajo de cada grupo.La sintaxis de la consulta será igual a las vistas previamente, es decir:
SELECT grupo, MIN(columna) FROM tabla GROUP by grupo

Select categoria, MIN(monto) as monto_mas_bajo from ventas group by categoria;

--Funciones de agregación y fechas
A la hora de construir informes, frecuentemente necesitaremos entregar información agrupada en un periodo de tiempo. Para lograr esto utilizaremos una combinación de GROUP BY con la función strftime.
Se nos solicita determinar el monto total de ventas por año. Para resolverlo tenemos que agrupar por fecha y sumar los montos de la siguiente forma:
SELECT SUM(monto), strftime("%Y", fecha_venta) AS año FROM ventas GROUP BY strftime("%Y", fecha_venta)

Select sum(monto) as suma_ventas, strftime('%m', fecha_venta) as mes from ventas group by strftime('%m', fecha_venta);

select strftime('%m',fecha_inscripcion) as mes, count(fecha_inscripcion) as cantidad_usuarios from inscripciones group by strftime('%m', fecha_inscripcion);

--Agrupando sin indicar el nombre de las columnas
Cuando se trata de agrupar datos en una consulta SQL, existe una forma de evitar la redundancia de la cláusula SELECT. Por ejemplo, considera la siguiente consulta:
SELECT strftime("%Y", fecha_venta) AS año, SUM(monto) FROM ventas GROUP BY strftime("%Y", fecha_venta)
Puedes simplificarla de la siguiente manera:
SELECT strftime("%Y", fecha_venta) AS año, SUM(monto) FROM ventas GROUP BY 1
Esta notación se interpreta como "agrupa por el primer criterio". También es posible aplicar esta sintaxis en la cláusula ORDER BY:
SELECT strftime("%Y", fecha_venta) AS año, SUM(monto) FROM ventas GROUP BY 1 ORDER BY 1
De esta manera, puedes lograr la misma agrupación y ordenamiento sin repetir la expresión de la cláusula SELECT.

select correo, count(correo) as repeticiones from usuarios group by 1 order by 1;

--Agrupando por múltiples columnas
En SQL es posible agrupar por múltiples columnas utilizando la siguiente sintaxis:
SELECT columna1, columna2, funcion_agrupado(columna3) FROM tabla GROUP BY columna1, columna2
Y como aprendimos en el ejercicio anterior, también podemos escribir la consulta de la siguiente manera:
SELECT columna1, columna2, funcion_agrupado(columna3) FROM tabla GROUP BY 1, 2

Calcula el promedio de cada estudiante en cada materia. Las columnas deben llamarse correo, materia y promedio_notas
select correo, materia, avg(nota) as promedio_notas from estudiantes group by 1,2;

-- Introducción a Having
En SQL, la cláusula GROUP BY nos permite agrupar datos. Si queremos filtrar la información obtenida utilizaremos HAVING.
HAVING se emplea para filtrar los resultados de una consulta que involucra funciones agregadas. En otras palabras, HAVING permite aplicar condiciones de filtrado a los resultados de funciones como COUNT, MAX, MIN, SUM y AVG después de que se han agrupado los datos con la cláusula GROUP BY.
Nos piden crear un reporte mostrando los meses y la cantidad de inscritos, pero solo donde hayan 2 o más inscritos.

SELECT strftime("%m", Fecha_Inscripcion) AS mes, COUNT(Fecha_Inscripcion) cantidad_usuarios 
FROM inscripciones 
GROUP BY strftime("%m", Fecha_Inscripcion)
HAVING cantidad_usuarios >= 2
En esta consulta, primero utilizamos GROUP BY para agrupar por mes. Luego, utilizamos la función de agregación COUNT(Fecha_Inscripcion) para contar la cantidad de inscritos.Después de haber agrupado los datos y calculado el total de inscritos, aplicamos la cláusula HAVING para filtrar los resultados.

Select strftime('%m', fecha_inscripcion) as mes, count(fecha_inscripcion) as cantidad_usuarios from inscripciones
group by strftime('%m', fecha_inscripcion)
having cantidad_usuarios = 1; 

--Buscando duplicados
Uno de los usos mas recurrentes de HAVING es buscar duplicados.
Muestra los correos que aparezcan en más de una ocasión. La tabla resultante debe tener dos columnas: una llamada correo, y otra llamada cuenta_correos que muestra la cantidad de repeticiones correspondiente a cada correo.

select correo, count(correo) as cuenta_correos from correos_corporativos
group by correo
having cuenta_correos > 1;

--Having y cuenta
Crea una consulta que muestre la cantidad de usuarios y el departamento en donde haya más de un empleado. Las columnas deben llamarse cantidad_de_usuarios y departamento, respectivamente.

select count(nombre) as cantidad_de_usuarios, departamento from empleados
group by departamento
having cantidad_de_usuarios > 1;

--Having y promedio
Crea una consulta para determinar cuales son los estudiantes que aprobaron. El criterio de aprobación es promedio de notas >= 50.
Las columnas a mostrar deben ser email y promedio_notas.

select email, avg(notas) as promedio_notas from notas
group by email
having promedio_notas >= 50;

--Having y order
Una vez que hemos agrupado datos utilizando la cláusula GROUP BY, es común que necesitemos ordenar esos grupos según algún criterio específico. Por lo general, queremos ordenar los grupos en función de alguna métrica agregada, como la suma, el conteo, el promedio, etc. Para hacer esto, usamos la cláusula ORDER BY junto con las funciones de agregación.
El orden de las clausulas en una consulta debe ser el siguiente:
Orden	Clausula	Descripción
1	SELECT	Especifica las columnas que se deben retornar en el resultado.
2	FROM	Especifica las tablas de las cuales se extraerán los datos.
3	WHERE	Filtra registros antes de cualquier agregación o agrupación.
4	GROUP BY	Agrupa registros por una o más columnas.
5	HAVING	Filtra registros después de la agregación.
6	ORDER BY	Ordena los registros retornados por una o más columnas.
7	LIMIT	Limita el número de registros retornados.

Dada la siguiente tabla ventas, escribe una consulta SQL para obtener los productos que se han vendido en una cantidad total mayor a 1000, ordenados en orden descendente de cantidad vendida.

select producto, sum(cantidad) as cantidad_total from ventas
group by producto
having cantidad_total > 1000 order by 2 desc;

Tu tarea es escribir una consulta SQL que devuelva los departamentos cuyo salario promedio es mayor a 3000, ordenados de mayor a menor salario promedio. Los resultados deben mostrar el nombre del departamento y el salario promedio, con los nombres de las columnas como Departamento y Salario_Promedio respectivamente.

Select Departamento, AVG(salario) as Salario_Promedio from empleados
group by Departamento
having Salario_Promedio > 3000 order by 2 desc;

--Subconsultas
--Introduccion a subconsultas
Las subconsultas, también conocidas como "subqueries", nos permiten utilizar los resultados de una consulta dentro de otra consulta.
Se nos pide seleccionar a todas las personas que ganan sobre el promedio.
Este tipo de preguntas podemos contestarlas utilizando subconsultas.
La idea para contestar esto es la siguiente.
Calculamos el promedio SELECT avg(sueldo) FROM empleados
Seleccionamos todos los empleados cuyo sueldo es mayor a la consulta anterior. SELECT * FROM empleados WHERE sueldo > (SELECT AVG(sueldo) FROM empleados)

select * from empleados where sueldo <= (select avg(sueldo) from empleados)

--Subconsultas y where parte 1
Dentro de las subconsultas, podemos utilizar las mismas cláusulas que hemos aprendido hasta ahora, como la cláusula WHERE. Esto significa que podemos aplicar la cláusula WHERE tanto dentro de la subconsulta como fuera de ella.

Selecciona toda la información de los registros que sean mayores al sueldo promedio del departamento de finanzas.
Select * from empleados where sueldo > (select avg(sueldo) from empleados where departamento = 'Finanzas')

Utilizando los datos de la tabla empleados, selecciona todos los empleados cuyo sueldo sea mayor al empleado que tiene el mayor sueldo del departamento de finanzas.
Select * from empleados where sueldo > (select max(sueldo) from empleados where Departamento = 'Finanzas')

Selecciona todos los registros superiores al promedio de nota.
Select * from notas where notas > (Select avg(notas) from notas)

--Subconsultas con IN
El operador IN es un operador muy útil en subconsultas
Queremos seleccionar todos los códigos de Argentina, Brasil, Chile o Colombia. Una forma de abordar el problema sería combinar todas las opciones con where y múltiples operadores or. Otra opción es utilizando el operador IN de la siguiente manera:
SELECT * 
FROM paises 
WHERE pais IN ('Argentina', 'Brasil', 'Chile', 'Colombia')
De la misma forma podemos hacer una consulta como la siguiente:
SELECT *
FROM table
WHERE columna IN (SELECT * from otra_tabla)

Se nos pide mostrar los nombres de todas las personas que tengan un promedio de notas menor que 50.
Seleccionamos los ids de la tabla notas con promedio_notas <= 50
Seleccionamos los nombres de de la tabla estudiantes cuyo id esté dentro de la subconsulta anterior.
SELECT nombre from estudiantes
WHERE estudiante_id IN (SELECT estudiante_id from notas where promedio_notas <= 50)

Select nombre from estudiantes 
where estudiante_id IN (Select estudiante_id from promedios where promedio_notas > 50)

Crea una consulta que muestre todos los títulos con valoración_promedio > 4. La columna resultante debe llamarse nombres_seleccionados.
Select nombre as nombres_seleccionados from libros 
where libro_id in (Select libro_id from valoraciones where valoracion_promedio > 4)

Se pide obtener los nombres de todos los pacientes que tuvieron su última consulta antes del 16 de mayo de 2023. La columna se debe llamar nombres_pacientes.
Select nombre as nombres_pacientes from pacientes 
where paciente_id in (select paciente_id from consultas where fecha_consulta < '2023-05-16')

--Subconsultas en el FROM
Una subconsulta en el FROM tiene la siguiente forma.

SELECT * 
    FROM (
        SELECT * FROM tabla1
)
Se tiene la tabla ventas que tiene el código de vendedor y el monto de cada venta realizada. Nos piden saber cuanto es el promedio total vendido.
Para esto primero necesitamos sumar los montos por vendedor y luego sobre estos resultados sacamos el promedio de las ventas.

SELECT AVG(total_venta) as promedio_ventas
FROM (
    SELECT empleado_id, SUM(monto) as total_venta
    FROM ventas
    GROUP BY empleado_id
) 

Select avg(total_venta) as promedio_ventas
from (
select empleado_id, sum(monto) as total_venta
from ventas
group by 1
)

Se tiene la tabla goles que registra los goles logrados por cada jugador en distintos partidos.
Nos piden una consulta para calcular el promedio total de goles por jugador.
Tip: No es lo mismo que el promedio de goles por partido.
Select avg(total_goles) as promedio_goles
from (
Select jugador_id, sum(goles) as total_goles
from goles
group by 1
)

--Combinacion de consultas
-- Introducción a la cláusula unión de SQL
El operador UNION en SQL se utiliza para combinar el resultado de dos o más SELECT en un solo conjunto de resultados.
La sintaxis básica de UNION es la siguiente:
SELECT columna1, columna2
FROM tabla1 
UNION SELECT columna1, columna2
FROM tabla2; 
Las columnas que se seleccionan en los SELECT deben tener los mismos nombres de columna, secuencia y tipos de datos.
SELECT apellido 
FROM Estudiantes 
UNION 
SELECT apellido 
FROM Profesores; 

Select nombre as nombres from estudiantes
Union
Select nombre from profesores;

--Eliminar duplicados con union
La principal característica de UNION es que elimina las filas duplicadas del resultado final.

Select email as correos_unicos from usuarios
Union
Select email from clientes;

--Union vs Union all
En los ejercicios anteriores aprendimos que el operador UNION se utiliza para combinar los resultados de dos o más consultas SELECT en un solo conjunto de resultados, eliminando las filas duplicadas.
Si queremos obtener las filas duplicadas en el resultado, utilizaremos el operador UNION ALL.
Podemos combinar ambas tablas utilizando UNION ALL de la siguiente forma:
SELECT * FROM tabla1 UNION ALL SELECT * FROM tabla2;

Ejercicio:
Dadas las siguientes tablas empleados1 y empleados2

empleados1
Nombre	Apellido	Edad
Juan	Pérez	30
María	González	25
Carlos	Rodríguez	40

empleados2
Nombre	Apellido	Edad
Ana	Martínez	22
María	González	25
Carmen	López	25
Crea una consulta que combine ambas tablas incluyendo las filas duplicadas.

Select * from empleados1
Union all
Select * from empleados2

--Introducción a intersección
El operador INTERSECT se utiliza para combinar dos SELECT y devolver los resultados que se encuentran en ambas consultas.
Podemos encontrar los clientes en común utilizando INTERSECT de la siguiente forma:
SELECT nombre FROM clientes1 INTERSECT SELECT nombre FROM clientes2

Select * from lista1
intersect
Select * from lista2

--El operador Except
El operador EXCEPT en SQL se utiliza para devolver todas las filas en la primera consulta que no están presentes en la segunda consulta. En otras palabras, EXCEPT devuelve solo las filas, que son parte de la primera consulta pero no de la segunda consulta.
Podemos usar EXCEPT para encontrar los nombres que están en 'Tabla1' pero no en 'Tabla2' con la siguiente consulta:
SELECT nombre FROM Tabla1 EXCEPT SELECT nombre FROM Tabla2;

Crea una consulta que muestre los nombres de los empleados que no son gerentes.
Select nombre from empleados
except
Select nombre from gerentes;

--Inserción de Registros
--Añadir un registro en una tabla
Con SQL podemos ingresar datos nuevos a tablas existentes. Para lograrlo utilizaremos la instrucción INSERT .
La instrucción INSERT la acompañaremos de las palabra clave INTO para especificar en qué tabla queremos insertar un valor y VALUES para especificar los valores que queremos insertar.
Por ejemplo. Si tenemos una tabla llamada productos con las columnas id, nombre y precio, podemos agregar un nuevo producto a la tabla usando utilizando:
INSERT INTO productos VALUES (1, 'Camiseta', 2000);
Para cada columna en la tabla debemos ingresar los valores correspondientes en el mismo orden en que se definen en la sentencia. Debemos utilizar comillas simples para valores de tipo de datos de texto.

Insert into usuarios values (7,'Lucía','Sánchez','luciasanchez@outlook.com','555-5555');
Insert into productos values (7,'Bolso',1000,10);

--Especificando valores nulos
A la hora de insertar datos, si hay un valor que no conocemos, o es un valor que no queremos especificar, podemos ingresar un valor nulo.
INSERT INTO productos VALUES (1, 'Camiseta', NULL, NULL);

Insert into productos values (7,'Bolso',1000,NULL);

--Añadir un registro especificando columnas
A la hora de insertar datos es posible mencionar específicamente las columnas que se van a insertar, en lugar de mencionar todos los valores en el orden en que se definen en la tabla.
Se pide insertar un nuevo producto con los siguientes datos, pero especificando las columnas
id: 7
nombre: Bolso
Precio: 1000
Stock: 10
INSERT INTO productos (id, precio, nombre, stock) VALUES (7, 1000,'Bolso', 10);
Una ventaja de este método es que no es necesario ingresar los valores en el mismo orden en que se definen en la tabla.

Insert into usuarios (id, apellido, nombre, telefono, email) values (7, 'Sánchez','Lucía','333-3333','luciasanchez@outlook.com');

--Añadir un registro especificando solo algunas columnas
Otro beneficio de especificar las columnas al momento de insertar datos es que se insertarán valores nulos en las columnas no mencionadas automáticamente.
Podemos ingresar el producto "Gorro" con un precio de 1000 y dejar el stock en nulo de la siguiente manera:
INSERT INTO productos (nombre, precio) VALUES ('Gorro', 1000);

Insert into productos (nombre, stock) values ('Bolso',10);

--Añadir fecha de hoy a un registro
Si queremos insertar la fecha actual al momento de crear un registro, podemos utilizar la función CURRENT_DATE para obtenerla.
Ejemplo: INSERT INTO usuarios (nombre, fecha_creacion) VALUES ('Gonzalo', CURRENT_DATE);

insert into productos (nombre, stock, fecha) values ('Bolso', 10, CURRENT_DATE);

--Añadiendo fecha y hora al insertar
Si queremos insertar una fecha cualquiera al momento de crear un registro, simplemente debemos hacerlo especificando la fecha en el formato esperado.
El formato de fecha es: YYYY-MM-DD, o sea año-mes-día, donde el año es de 4 dígitos, el mes es de 2 dígitos y el día es de 2 dígitos.
Ejemplo: INSERT INTO usuarios (nombre, fecha_creacion) VALUES ('Gonzalo', '2021-01-01');

Inserta un nuevo producto con los siguientes datos:
nombre: Bolso
stock: 10
fecha: fecha_con_formato
La fecha del producto debe ser del primero de enero del 2023.
Insert into productos (nombre, stock, fecha) values ('Bolso', 10, '2023-01-01');

-- Añadir múltiples valores
Podemos ingresar varios registros en una tabla en una sola sentencia INSERT. Para lograrlo, debemos especificar los valores de cada registro separados por comas.
Por ejemplo, si tenemos una tabla llamada ventas con las columnas producto, cantidad y precio, podemos agregar varios registros a la tabla usando:
INSERT INTO ventas VALUES ('Camiseta', 5, 2000), ('Pantalón', 3, 1500), ('Zapatos', 2, 3000);

Insert into ventas values ('Gorro', 5, 1000),('Camiseta', 10, 500),('Pantalón', 8, 1500);

--Crear un registro con un campo autoincremental
En una base de datos SQL, es posible agilizar el proceso de inserción de datos en una tabla mediante el uso de un campo autoincremental. Este tipo de campo es especialmente útil cuando se trata de gestionar identificadores únicos, como por ejemplo el campo "id" de una tabla. La característica de autoincremento se logra empleando la cláusula AUTOINCREMENT en la definición del campo.

Para ilustrar este proceso, consideremos una tabla llamada "empleados" con tres columnas: "id" (autoincremental), "nombre" y "apellido". Esta es la forma en que se crea la tabla:
CREATE TABLE empleados (id INTEGER PRIMARY KEY AUTOINCREMENT, nombre TEXT,apellido TEXT);
Aquí, hemos definido la columna "id" como un campo autoincremental utilizando la cláusula AUTOINCREMENT, lo que asegura que se generará automáticamente un valor único y creciente para cada nuevo registro.
Supongamos que deseamos insertar un nuevo empleado en esta tabla. Podemos utilizar la siguiente consulta SQL:
INSERT INTO empleados (nombre, apellido) VALUES ('John', 'Doe');
Al ejecutar esta consulta, se creará un nuevo empleado en la tabla "empleados". La columna "id" se incrementará automáticamente, mientras que los valores proporcionados para "nombre" y "apellido" serán almacenados en las columnas correspondientes. Esto garantiza que cada nuevo empleado tendrá un identificador único y que el proceso de inserción sea más eficiente.

Dada la tabla empleados con las columnas id, nombre y apellido, crea un nuevo empleado con el nombre "Jane" y el apellido "Smith".
Insert into empleados (nombre, apellido) values ('Jane','Smith');

--Añadir un registro asumiendo un valor por defecto
Al crear una tabla en SQL, puedes asignar valores predeterminados a sus columnas. Esto implica que al insertar nuevos datos, si no se proporciona un valor específico para una columna, se usará automáticamente el valor por defecto asignado.
Supongamos que queremos crear una tabla llamada "Productos" con las siguientes columnas:
ID (identificador único del producto)
Nombre (nombre del producto)
Precio (precio del producto, con un valor por defecto de 10)
CREATE TABLE Productos (ID INTEGER PRIMARY KEY AUTOINCREMENT, Nombre TEXT, Precio INTEGER DEFAULT 10);
Si insertamos un nuevo producto sólo con el nombre, se utilizará automáticamente el valor por defecto del precio:
INSERT INTO Productos (Nombre) VALUES ('Ejemplo Producto');
En este caso, el producto se insertará con el valor 10 en la columna Precio.
Si deseamos insertar un producto con un precio diferente, simplemente proporcionamos el valor correspondiente:
INSERT INTO Productos (Nombre, Precio) VALUES ('Otro Producto', 25);

Dada la tabla usuarios con las columnas id, nombre, apellido, email y telefono, crea un nuevo usuario con los valores:
nombre: Lucía
apellido: Sánchez
email: luciasanchez@outlook.com
La columna telefono tendrá el valor por defecto 111-1111
Insert into usuarios (nombre, apellido, email) values ('Lucía', 'Sánchez', 'luciasanchez@outlook.com')

--Borrado y modificación de registros
--Borrar todos los registros de una tabla
En SQL, la cláusula DELETE se utiliza para eliminar registros de una tabla. Cuando se ejecuta la instrucción DELETE FROM nombre_tabla, se eliminan todos los registros de la tabla especificada.
Es importante tener en cuenta que esta operación es irreversible y eliminará permanentemente los datos de la tabla, por lo que debes tener mucho cuidado al usar esta instrucción.

Delete from productos;
Note: en el delete no se incluye el *

--Borrar un registro con where
La sentencia DELETE se utiliza para eliminar datos de una tabla. Si queremos eliminar filas específicas en lugar de todos los datos de la tabla, podemos usar la cláusula WHERE junto con la sentencia DELETE. Esto nos permite especificar una condición para determinar qué filas se eliminarán.
Por ejemplo, si tenemos una tabla de productos y queremos eliminar solo aquellos productos cuyo precio sea igual a 1000, podemos usar la siguiente consulta:
DELETE FROM productos WHERE precio = 1000

Delete from usuarios where id = 2;

--Editar registros
La sentencia UPDATE se utiliza para realizar modificaciones en datos ya existentes de una tabla.
Se utiliza de la siguiente forma
UPDATE nombre_tabla SET nombre_columna = nuevo_valor
Supongamos que tenemos una tabla ventas con una columna llamada "total". Si queremos aumentar en un 10% el total de todas las ventas registradas en la tabla, podemos hacerlo de la siguiente manera:
UPDATE ventas SET total = total * 1.10;
La instrucción UPDATE afecta todas las filas de la tabla, ya que no hemos utilizado la cláusula WHERE para establecer una condición de filtro.

Edita la columna "registrado" para que todos los usuarios tengan el valor TRUE
Update usuarios set registrado = TRUE;

--Editar todos los registros utilizando where
Si queremos editar solamente algunas filas de nuestra tabla, podemos utilizar UPDATE en conjunto con WHERE. De esta forma solo se modificarán los registros que cumplan con la condición especificada.
UPDATE nombre_tabla SET nombre_columna = nuevo_valor WHERE condicion;
Supongamos que gestionamos una tabla llamada empleados que contiene información sobre los empleados de una empresa. Entre las columnas se encuentran id_empleado, nombre, salario y departamento. Si deseamos aumentar el salario en un 15% solamente para los empleados que trabajan en el departamento de "Ventas", podríamos emplear la instrucción UPDATE junto con WHERE de la siguiente manera:
UPDATE empleados SET salario = salario * 1.15 WHERE departamento = 'Ventas';
Es importante ser precavido al elegir la condición de filtrado para tus filas. De esta manera, te aseguras de no alterar accidentalmente datos equivocados.

Update usuarios set telefono = '123-456' where id = 4;

--Editar múltiples columnas
En SQL es posible editar múltiples columnas de un registro utilizando la cláusula SET. Para lograrlo, debemos especificar el nombre de cada columna que queremos modificar, seguido del nuevo valor que queremos asignarle.
UPDATE tabla 
  SET 
    columna1 = 'nuevo_valor',
    columna2 = 'nuevo_valor',
    columna3 = 'nuevo_valor'
  WHERE 
    condicion;

Ejercicio:
Se tiene una tabla de posts con las siguientes columnas:
Columna	Tipo de dato
id	INTEGER
titulo	TEXT
contenido	TEXT
autor	TEXT
fecha	TEXT
Edita el post con id 1 para que tenga el título "Aprendiendo SQL" y el contenido "SQL es un lenguaje de programación para gestionar bases de datos relacionales".

Update posts
set
    titulo = 'Aprendiendo SQL',
    contenido = 'SQL es un lenguaje de programación para gestionar bases de datos relacionales'
where id = 1;

--Tables
--Nuestra primera tabla
Para crear una tabla en SQL, se utiliza la sentencia CREATE TABLE de la siguiente forma:
CREATE TABLE nombre_tabla (nombre_columna1 tipo_de_dato) 
Esta sentencia permite definir la estructura de la tabla, incluyendo el nombre de las columnas y sus tipos de datos. Veamos un ejemplo de cómo crear una tabla de productos que incluye diferentes tipos de datos para las columnas:
CREATE TABLE productos (nombre TEXT);
Luego, una vez creada la tabla, podemos insertar datos tal como aprendimos en ejercicios anteriores:
INSERT INTO productos values   ('Ipad Pro 2022'),  ('Iphone 13 Pro Max'),  ('Macbook Pro 2023');

Ejercicio:
Crea una tabla llamada alumnos que almacene una columan nombre de tipo texto
Columna	Tipo de dato
nombre	texto
Inserta un registro dentro de la tabla creada utilizado los siguientes datos:
nombre: Lucía

Create table alumnos (nombre text);
Insert into alumnos values ('Lucía');

--Una tabla con múltiples columnas
Al momento de crear una tabla podemos especificar múltiples columnas, cada una con su nombre y tipo de dato. Por ejemplo, si queremos crear una tabla de productos que incluya el nombre, descripción y precio de cada producto, podemos hacerlo de la siguiente forma:
CREATE TABLE productos (nombre TEXT, descripcion TEXT, precio INT);

Ejercicio
Crea una tabla llamada alumnos con las siguientes columnas:

Columna	Tipo de dato
nombre	texto
apellido	texto
telefono	texto
Inserta un registro dentro de la tabla creada utilizado los siguientes datos:
nombre: Lucía
apellido: Sánchez
telefono: 12345678

Create table alumnos (nombre text, apellido text, telefono text);
Insert into alumnos values ('Lucía','Sánchez','12345678');

--Tablas con distintos tipos de datos
Adicionalmente a los datos de tipo Texto podemos utilizar otros tipos de datos, en este ejercicio abordaremos los 3 siguientes tipos.
INTEGER para almacenar números enteros
BOOLEAN para almacenar valores de verdadero o falso
DATE para almacenar fechas

Crea una tabla llamada usuarios con las siguientes columnas:
Columna	Tipo de dato
nombre	text
apellido	text
edad	integer
activo	boolean
nacimiento	date
Luego inserta un registro dentro de la tabla creada utilizado los siguientes datos:
nombre: Lucía
apellido: Sánchez
edad: 25
activo: true
nacimiento: 1996-01-01

Create table usuarios (nombre text, apellido text, edad integer, activo boolean, nacimiento date);
Insert into usuarios values ('Lucía','Sánchez', 25, true, '1996-01-01');

--Tipos reales
En este ejercicio veremos el tipo de dato REAL, que permite almacenar números con decimales.

Ejercicio:
Crea una tabla llamada temperaturas con la columna temperatura_celsius:

Columna	Tipo de dato
temperatura_celsius	real
Luego inserta los siguientes registros:
temperatura_celsius
23.4
26.5
27.1
Importante. Para ingresar la parte decimal de los números, utiliza el punto (.) como separador decimal

create table temperaturas (temperatura_celsius double);
Insert into temperaturas values (23.4), (26.5), (27.1);

--Borrar un tabla
En SQL podemos utilizar el comando DROP TABLE para eliminar una tabla.
Por ejemplo, si queremos eliminar la tabla temperaturas que creamos en el ejercicio anterior, podemos hacerlo con la siguiente query:
DROP TABLE temperaturas;
Si intentamos hacer un SELECT de la tabla temperaturas luego de eliminarla, obtendremos un error.

Ejercicio:
En este ejercicio tendremos una tabla con datos que no nos interesan, deberemos borrarla, crearla de nuevo y poblarla con los datos pedidos.
Borra la tabla temperaturas y vuelve a crearla con las siguientes columnas:
Columna	Tipo de dato
ciudad	TEXT
temperatura	REAL
fecha	Date
Luego, inserta los siguientes registros:
ciudad	temperatura	fecha
Buenos Aires	20.0	2024-01-01
Buenos Aires	21.0	2024-01-02
Santiago	22.0	2024-01-01
Santiago	23.0	2024-01-02

drop table temperaturas;
Create table temperaturas (ciudad text, temperatura double, fecha date);
Insert into temperaturas 
values 
('Buenos Aires', 20.0, '2024-01-01'), 
('Buenos Aires', 21.0, '2024-01-02'), 
('Santiago', 22.0, '2024-01-01'),
('Santiago', 23.0, '2024-01-02');

--Actualizar una tabla
En este ejercicio aprenderemos a añadir una columna a una tabla existente. Para ello, utilizaremos la sentencia ALTER TABLE, que nos permite modificar la definición de una tabla.
La sintaxis para lograrlo es la siguiente:
ALTER TABLE nombre_tabla ADD COLUMN nombre_columna tipo_dato;
donde tenemos que especificar el nombre de la tabla existente, el nombre de la columna nueva y el tipo de dato que utilizaremos.
Por ejemplo si tenemos la tabla personas con las columnas nombre y apellido, y queremos agregar la columna edad de tipo INTEGER, podemos hacerlo de la siguiente manera:
ALTER TABLE personas ADD COLUMN edad INTEGER;

Ejercicio:
En este ejercicio, vamos a modificar la tabla productos para agregar la columna descripcion de tipo TEXT.
Actualmente la tabla productos tiene las siguientes columnas:
Columna	Tipo de dato
nombre	TEXT
precio	REAL
Luego de crearla deberás insertar los siguientes registros:
nombre	precio	descripcion
Camisa	1000.00	Camisa de manga corta
Pantalón	2000.00	Pantalón de mezclilla
Camisa XL	1000.00	Camisa de manga larga

Alter table productos add column descripcion text;
Insert into productos 
values
    ('Camisa', 1000.00, 'Camisa de manga corta'),
    ('Pantalón', 2000.00, 'Pantalón de mezclilla'),
    ('Camisa XL', 1000.00, 'Camisa de manga larga');
