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

--Restrictions - Constrains
--Introducción a restricciones
Ideas clave
Las restricciones o constraints en inglés, son reglas que se pueden aplicar a las columnas de una tabla.
La restricción NOT NULL es un tipo de restricción que impide que se ingresen valores nulos en una columna.
Si ingresamos un valor nulo en una columna con la restricción NOT NULL, la operación fallará.
Añadir una restricción al crear una tabla
Al crear tablas, podemos añadir restricciones (en inglés constraints) a las columnas para evitar que se ingresen datos que no cumplan ciertas condiciones.
En este ejercicio, aprenderemos a agregar la restricción NOT NULL, que impide agregar valores nulos en una columna. Por ejemplo, al crear una tabla de personas con nombre y apellido, podemos hacer que el nombre sea obligatorio (no nulo) y el apellido opcional.
Para lograrlo, crearemos la tabla y en la columna nombre agregaremos la restricción NOT NULL de la siguiente manera:
CREATE TABLE personas (
    nombre TEXT NOT NULL,
    apellido TEXT
)
Para agregar una restricción, simplemente debemos especificarla junto con la columna.

create table empleados (nombre text not null, apellido text);
Insert into empleados values ('Pedro', 'Pérez');

--Agregar una restricción a una tabla existente
Ideas clave
Las restricciones o constraints en inglés son reglas que se pueden aplicar a las columnas de una tabla.
Se puede agregar una restricción al momento de crear una tabla.
En SQLite no se pueden agregar directamente restricciones a tablas ya creadas.
Pero podemos lograrlo creando una nueva tabla con la restricción, copiando los datos de la tabla original a la nueva tabla, borrando la tabla original y renombrando la nueva tabla con el nombre de la tabla original.
Agregar una restricción a una tabla existente
Por ejemplo, si tenemos una tabla personas con las columnas nombre y apellido.
Columna	Tipo de dato	Restricciones
nombre	TEXT	
apellido	TEXT	
y queremos agregarle la restricción NOT NULL a la columna nombre.
Lo que tenemos que hacer es:
Crear una nueva tabla con la restricción.
/* 1. Creamos la nueva tabla con la restricción */
CREATE TABLE personas2 (
    nombre TEXT NOT NULL,
    apellido TEXT
);
Copiar los datos de la tabla original a la nueva tabla.
/* 2. Copiamos los datos de la tabla original a la nueva tabla */
INSERT INTO personas2 (nombre, apellido)
    SELECT nombre, apellido
    FROM personas;
Borrar la tabla original.
/* 3. Borramos la tabla original */
DROP TABLE personas;
Renombrar la nueva tabla con el nombre de la tabla original.
/* 4. Renombramos la nueva tabla con el nombre de la tabla original */
ALTER TABLE personas2 RENAME TO personas;
En otros motores de bases de datos como PostgreSQL o MySQL si es posible agregar restricciones a tablas existente sin necesidad de crear una nueva tabla.

Ejercicio
create table patentes2 (patente text not null);
Insert into patentes2 (patente)
    select patente from patentes;
Drop table patentes;
Alter table patentes2 rename to patentes;

--Borrar una restricción
Así como se pueden agregar restricciones a las columnas, también se pueden borrar.
Borrar una restricción de una tabla existente
Para borrar una restricción de una tabla existente en SQLite, se debe seguir un procedimiento similar al de agregar una restricción.
Crear una nueva tabla sin la restricción.
Copiar los datos de la tabla original a la nueva tabla.
Borrar la tabla original.
Renombrar la nueva tabla con el nombre de la tabla original.

Ejercicio
Create table personas2 (nombre text, apellido text, edad integer);
Insert into personas2 (nombre, apellido, edad)
    select nombre, apellido, edad
    from personas;
Drop table personas;
Alter table personas2 rename to personas;

--Restricción unique
Ideas clave
Existen distintos tipos de restricciones que se pueden aplicar a las columnas de una tabla.
La restricción de unicidad, o UNIQUE, nos permite evitar duplicados en una columna específica.
Un caso muy popular de esta restricción es evitar personas con el mismo correo electrónico.

Para agregar una restricción de UNIQUE simplemente tenemos que especificarla al crear la tabla. Por ejemplo, si queremos que el correo electrónico de las personas sea único, podemos crear la tabla de la siguiente manera:
CREATE TABLE personas (
    nombre text
    apellido text
    email text UNIQUE
)

Create table productos (nombre text not null, precio double not null, codigo text unique);
Insert into productos values
    ('Camisa', 1000.00, 'CAM-001'),
    ('Pantalón', 2000.00, 'PAN-001'),
    ('Camisa XL', 1000.00, 'CAM-002');

--Restricciones con check
Ideas clave
Existen distintos tipos de restricciones que se pueden aplicar a las columnas de una tabla.
La restricción de no nulidad, o NOT NULL, impide que se ingresen valores nulos en una columna.
La restricción de unicidad, o UNIQUE, nos permite evitar duplicados en una columna específica.
La restricción CHECK nos permite establecer una condición que los valores de una columna deben cumplir.
Restricciones CHECK
La restricción CHECK nos permite establecer una condición que los valores de una columna deben cumplir. Por ejemplo, si queremos que el salario de los empleados sea mayor a cero, podemos agregar una restricción de CHECK a la columna salario.
Para agregar una restricción de CHECK simplemente tenemos que especificarlo en la definición de la columna, proporcionando la condición que debe cumplir el valor de la columna. Por ejemplo:
CREATE TABLE empleados (
    nombre TEXT,
    salario REAL CHECK (salario > 0)
);

create table productos (nombre text not null, precio double not null, stock integer check(stock>=0));
Insert into productos values
('Camisa', 1000.00, 10),
('Pantalón', 2000.00, 5),
('Camisa XL', 1000.00, 3);

--Clave primaria
Existen distintos tipos de restricciones que se pueden aplicar a las columnas de una tabla como NOT NULL, UNIQUE y CHECK.
La restricción Primary Key impide que se ingresen valores nulos y asegura que no haya duplicados en una columna específica. Para efectos prácticos, podemos decir que es una combinación de Unique y Not Null
¿Qué es una clave primaria?
La clave primaria, o en inglés PRIMARY KEY es una restricción que sirve para identificar de forma única cada registro en una tabla. Por ejemplo supongamos que tenemos una tabla llamada boletas con los siguientes registros:
id	monto de la boleta	fecha de emision
1	10.000	2021-10-01
2	12.000	2021-10-02
3	16.000	2021-10-03
Y queremos buscar la boleta con id 2. Si no tuvieramos una clave primaria, podríamos tener dos boletas con el mismo id, y no sabríamos cuál de las dos es la que queremos, o podríamos tener boletas con id nulo, y no sabríamos cuál es la boleta que buscamos.
La restricción de primary key nos asegura esto no suceda.
Cuando tenemos una clave primaria, tenemos certeza de que podemos buscar cualquier registro en la base de datos y luego modificarlo o eliminarlo, y no habrá ningún otro registro modificado o eliminado que el seleccionado. Esto nos permite cuidar la integridad de los datos.
Agregar una clave primaria a una tabla nueva
Para agregar una clave primaria a una tabla nueva, simplemente tenemos que especificarlo en la definición de la columna. Por ejemplo:
CREATE TABLE boletas (
    id INT PRIMARY KEY,
    monto_de_la_boleta REAL,
    fecha_de_emision DATE
);

Create table posts (id int primary key, title text, content text);
Insert into posts values
    (1, 'Introducción', '¡Bienvenido al mundo de la programación!'),
    (2, 'Primeros Pasos', 'Sumérgete en los conceptos básicos de la programación.'),
    (3, 'Temas Avanzados', 'Explora conceptos y técnicas avanzadas en programación.');

--Autoincremental
Ideas clave
La restricción Primary Key impide que se ingresen valores nulos y asegura que no haya duplicados en una columna específica. Para efectos prácticos, podemos decir que es una combinación de Unique y Not Null.
Los campos autoincrementales nos permiten generar un valor único de forma automática para cada registro que insertemos en una tabla.
Si un campo de clave primaria es un número entero, se convierte automáticamente en un campo autoincremental en SQLITE.
¿Qué son los campos autoincrementales?
Los campos autoincrementales son campos que generan un valor único de forma automática para cada registro que insertemos en una tabla. Usualmente, el incremento es de 1 en 1.
Entonces si tenemos una tabla como la siguiente:
id	monto de la boleta	fecha de emision
1	10.000	2021-10-01
2	12.000	2021-10-02
3	16.000	2021-10-03
Y agregamos un nuevo registro sin especificar el valor del campo id, la base de datos se encargará de generar un valor único para ese campo, que para este caso sería 4.

Create table usuarios (id integer primary key, nombre text not null, fecha_creacion date);
Insert into usuarios (nombre, fecha_creacion) values
    ('Ana', '2024-01-01'),
    ('Gonzalo', '2024-01-02'),
    ('Juan', '2024-01-03'),
    ('María', '2024-01-04');
Note: the autoincrement word is optional in SQLlite in the table definition. In fact, sometimes it can cause some issues.

--Autoincremental parte 2
Si se ingresa un registro con un valor mayor al de la secuencia actual, la base de datos se encarga de actualizar la secuencia para que el siguiente registro tenga un valor mayor al del registro que acabamos de insertar.
Por ejemplo, si tenemos una tabla con los siguientes registros:
id	nombre
1	Ana
2	Gonzalo
3	Juan
Luego insertamos un nuevo registro con un id mayor al de la secuencia actual:
INSERT INTO usuarios (id, nombre) VALUES (10, 'María');
Y luego insertamos un nuevo registro sin especificar el id:
INSERT INTO usuarios (nombre) VALUES ('Pedro');
Obtendremos la siguiente tabla:
id	nombre
1	Ana
2	Gonzalo
3	Juan
10	María
11	Pedro

Ejercicio:
Create table transacciones (id integer primary key, monto real not null, fecha date);
Insert into transacciones (monto, fecha) values
    (1000.00, '2024-01-01'),
    (2000.00, '2024-01-02'),
    (3000.00, '2024-01-03');
Insert into transacciones values (10, 4000.00, '2024-01-04');
Insert into transacciones (monto, fecha) values (5000.00, '2024-01-05');

--Primary key y texto
La clave primaria no está limitada exclusivamente a valores numéricos; también se pueden utilizar datos de texto. Tomemos, por ejemplo, una tabla de personas, donde podríamos emplear la dirección de correo electrónico como clave primaria, ya que cada individuo posee una dirección de correo única.
En SQLite, los campos que son de tipo INTEGER y se designan como PRIMARY KEY no pueden contener valores nulos. No obstante, a diferencia de otros sistemas de gestión de bases de datos como MySQL o PostgreSQL, cuando se utiliza PRIMARY KEY con tipos de datos como texto u otros, se permite que el valor sea nulo.
Por lo tanto, si queremos que un campo sea tanto clave primaria como no nulo, debemos especificarlo mediante la combinación de PRIMARY KEY y NOT NULL.
Ejemplo:
CREATE TABLE posts (
    title text primary key not null
)

Create table personas (email text primary key not null, nombre text, apellido text);
Insert into personas values
    ('example1@example.com','John', 'Doe'),
    ('example2@example.com','Jane', 'Smith'),
    ('example3@example.com','Mike', 'Johnson');

--Clave Foránea
La clave foránea es una restricción que se le puede agregar a una columna de una tabla para indicar que los valores que se inserten en esa columna deben existir en otra tabla.
Por ejemplo, si tenemos una tabla de personas y una tabla de autos, podríamos agregar una columna persona_id a la tabla de autos, y agregarle la restricción de clave foránea para indicar que el valor de esa columna debe existir en la tabla de personas. De esta forma nos aseguramos que no se inserten autos de personas que no existen o que se borren personas que tienen autos asignado a su nombre dejando autos sin dueño.
personas
Columna	Tipo de dato	Restricciones
id	INTEGER	PRIMARY KEY
nombre	TEXT	
apellido	TEXT	
autos
Columna	Tipo de dato	Restricciones
id	INTEGER	PRIMARY KEY
patente	TEXT	
persona_id	INTEGER	FOREIGN KEY (persona_id) REFERENCES personas(id)
Con los siguientes datos:
personas
id	nombre	apellido
1	John	Doe
2	Jane	Smith
autos
id	patente	persona_id
1	ABC123	1
2	DEF456	2
Podemos ver que el auto con patente ABC123 pertenece a la persona con id 1, y el auto con patente DEF456 pertenece a la persona con id 2. Adicionalmente la clave foránea nos asegura que no podamos borrar la persona con id 1 mientras exista un auto con persona_id 1. De la misma forma, no podremos insertar un auto con persona_id 3, ya que no existe una persona con id 3.
Agregando la clave foránea
Para agregar una clave foránea a una tabla existente, debemos especificar la restricción FOREIGN KEY seguida del nombre de la columna y la tabla a la que hace referencia, y finalmente la columna de la tabla a la que hace referencia.
La sintaxis es la siguiente:
ALTER TABLE nombre_tabla ADD COLUMN nombre_columna tipo_dato REFERENCES nombre_tabla_referencia(nombre_columna_referencia);
Se ve complicado, pero veamos un ejemplo con las tablas personas y autos.
ALTER TABLE autos ADD COLUMN persona_id INTEGER REFERENCES personas(id);
La clave foránea debe hacer referencia a una columna que tenga una restricción de clave primaria

Ejercicio:
Se pide agregar una clave foránea a la tabla articulos para que la columna categoria_id haga referencia a la columna id de la tabla categorias.
Alter table articulos add column categoria_id integer references categorias(id);

--Pk y fks
Ejercicio:
Se tiene la tabla transacciones y la tabla usuarios con la siguiente estructuras:
transacciones
Columna	Tipo de dato	Restricciones
id	INTEGER	PRIMARY KEY
monto	REAL	
usuario_id	INTEGER	FOREIGN KEY (usuario_id) REFERENCES usuarios(id)
usuarios
Columna	Tipo de dato	Restricciones
id	INTEGER	PRIMARY KEY
nombre	TEXT	
apellido	TEXT	
Con los siguientes datos:
transacciones
id	monto	usuario_id
1	100	1
2	200	2
3	300	1
usuarios
id	nombre	apellido
1	John	Doe
2	Jane	Smith
En este ejercicio primero intentaremos crear una transacción con un usuario que no existe para observar el error.
Intentaremos borrar un usuario que tiene transacciones asociadas para observar el error.
Luego eliminaremos nuestras consultas anteriores y modificaremos la tabla de transacciones para eliminar la clave foránea. Solo se debe eliminar la clave foránea, no la columna.
TIP: Esto requiere crear una tabla temporal, copiar los datos de la tabla original a la tabla temporal, borrar la tabla original, y renombrar la tabla temporal con el nombre de la tabla original.
Finalmente se deben asociar las transacciones al usuario con id 3. El cual no existe y la idea es demostrar que sin la FK podemos insertar transacciones sin usuarios asociados.
Los puntos 1 y 2 son para observar que sucede. Para lograr la respuesta correcta tienes que realizar los puntos 3 y 4 en el editor.

Insert into transacciones (monto, usuario_id) values (300, 3);
Delete from usuarios where id = 1;
Create table transacciones2 (id integer primary key, monto real, usuario_id integer);
Insert into transacciones2 (id, monto, usuario_id)
    select id, monto, usuario_id from transacciones;
drop table transacciones;
Alter table transacciones2 rename to transacciones;
Update transacciones set usuario_id = 3;

--Consultas en multiples tablas
Ideas clave
La cláusula JOIN permite combinar datos de varias tablas en una única tabla de resultados.
Para realizar la unión, es necesario especificar las tablas que se desean unir y la condición de unión correspondiente.
SELECT *
FROM 
    usuarios
INNER JOIN 
    datos_contacto
ON 
    email1 = email2;

Ejercicio:
Utilizando lo aprendido, selecciona todos los usuarios junto a sus notas. Observa los resultados antes de avanzar.
Select *
from usuarios inner join notas
on email1 = email2;

--Multiples tablas: Utilizando atributos del mismo nombre
Cuando la columna se llama igual en las dos tablas, tenemos que especificar el nombre de la tabla para evitar ambigüedad.
Para arreglar esto NO tenemos que cambiarle el nombre a la columna, simplemente tenemos que especificar a qué tabla pertenece el atributo. Por ejemplo, podemos hacer lo siguiente:
SELECT * 
FROM 
    usuarios 
JOIN 
    datos_contacto 
ON 
    usuarios.email = datos_contacto.email

Alias
En SQL, podemos utilizar alias para referirnos a las tablas de una forma más corta. Por ejemplo, si queremos referirnos a la tabla usuarios como u y a la tabla datos_contacto como dc, podemos hacer lo siguiente:
SELECT * 
FROM 
    usuarios u
JOIN 
    datos_contacto dc 
ON 
    u.email = dc.email

Ejercicio:
Select *
from 
    usuarios u
inner join 
    notas n
on
    u.email = n.email;

--Seleccionando algunos atributos
Podemos seleccionar los datos de cada tabla que deseamos mostrar en la consulta.
>Seleccionando algunos atributos
Al igual que cuando trabajamos con una sola tabla, podemos seleccionar sólo los atributos que deseamos mostrar en la consulta. Cuando tenemos dos tablas, podemos seleccionar todos los atributos de una tabla y sólo algunos de la otra de la siguiente forma:
SELECT tabla1.*, 
    tabla2.atributo1, 
    tabla2.atributo2
FROM tabla1
JOIN tabla2 ON tabla1.id = tabla2.id;

De esta forma, seleccionamos todo de la tabla usuarios y sólo los teléfonos de la tabla datos_contacto. Asi no tenemos dos columnas email con datos repetidos
SELECT usuarios.*, datos_contacto.telefono 
FROM usuarios 
JOIN datos_contacto 
ON usuarios.email = datos_contacto.email

Ejercicio:
Select usuarios.*, notas.notas
from
    usuarios
join
    notas
on
    usuarios.email = notas.email;

--JOIN sin resultados
La cláusula JOIN solo une los registros que tienen una clave común en ambas tablas. Existen otros tipos de JOIN que revisaremos más adelante.
La respuesta es bien sencilla: si no hay ningún dato común entre ambas tablas, no obtendremos resultados.
Se tiene email en ambas tablas pero los datos que tiene usuarios en email no estan en datos_contacto, por eso el resultado es empty
select
    usuarios.*,
    datos_contacto.telefono
from
    usuarios
join
    datos_contacto
on
    usuarios.email = datos_contacto.email;

--Orden de cláusulas
Las cláusulas tienen un orden específico que debemos seguir para que la consulta funcione correctamente.
Comando	Se lee como:
SELECT	Selecciona estos datos.
FROM	De esta tabla.
JOIN	Únelos con esta tabla.
WHERE	Filtra los valores que cumplan tal condición.
GROUP BY	Agrupa los resultados por este criterio.
HAVING	Filtra por estos criterios agrupados.
ORDER BY	Ordena los resultados por este otro criterio.
LIMIT	Limita los resultados a esta cantidad.

Ejercicio: Dadas las siguientes tablas, selecciona toda la información del usuario juan.perez@example.com.
Select *
from
    usuarios
join
    notas
on
    usuarios.email = notas.email
Where
    usuarios.email = 'juan.perez@example.com';

--Agrupar por múltiples columnas
Utilizando GROUP BY y funciones de agregación en consultas con múltiples tablas
Al igual que en las consultas sobre una tabla, podemos utilizar funciones de agregación y agrupado en consultas sobre múltiples tablas.
Supongamos que tenemos dos tablas: una tabla llamada clientes y otra tabla llamada pedidos
Tabla clientes:
cliente_id	nombre
1	Juan Pérez
2	Ana Gómez
3	Luis Fernández
Tabla pedidos:
pedido_id	cliente_id	fecha
101	1	2023-01-15
102	2	2023-02-20
103	1	2023-03-10
104	3	2023-04-22
105	2	2023-05-18
106	1	2023-06-30
Para mostrar la cantidad total de pedidos realizados por cada cliente, podemos usar la siguiente consulta SQL:

SELECT nombre, COUNT(p.pedido_id) AS total_pedidos
FROM clientes c
JOIN pedidos p ON c.cliente_id = p.cliente_id
GROUP BY c.cliente_id, nombre;

Algunos detalles importantes a tener en cuenta:
Si hay columnas con el mismo nombre entonces tenemos que especificar el nombre de la tabla antes del nombre de la columna.
A la hora de agrupar datos debemos tener en cuenta que las columnas que no están en la cláusula GROUP BY deben ser utilizadas con funciones de agregación. Por lo mismo, en este tipo de ejercicios, el SELECT * no es recomendable, especialmente si la tabla tiene muchas columnas.

Ejercicio: Tenemos dos tablas: Productos y Ventas. Realiza una consulta que nos muestre el producto más vendido y la cantidad total de unidades vendidas de ese producto. La columna que muestre el total de unidades vendidas debe llamarse "total_vendido"
Pista: Recuerda el uso de order by y limit
-debemos usr desc en el order by porque nos estan preguntando por el valor mas alto
Select nombre, sum(v.cantidad) as total_vendido
from
    productos p
join
    ventas v
on
    p.productoid = v.productoid
group by p.productoid, nombre
order by total_vendido desc limit 1;

--Cardinalidad
--Relaciones 1 a 1
**Ideas clave**
La cardinalidad de una relación es la cantidad de elementos de una tabla que pueden estar relacionados con los de otra tabla.
Dependiendo de como se modelen las relaciones entre tablas, se pueden tener relaciones de uno a uno, uno a muchos o muchos a muchos.
En una relación uno a uno, un registro de una tabla solo puede estar relacionado con un registro de la otra tabla.
**¿Qué es la cardinalidad de una relación?**
La cardinalidad de una relación es la cantidad de elementos de una tabla que pueden estar relacionados con los de otra tabla.
Las relaciones entre tablas se pueden clasificar en tres tipos principales según su cardinalidad.
En este ejercicio veremos la relación uno a uno, denotada como 1:1. En esta relación, cada registro de una tabla está relacionado con un único registro de otra tabla. Por ejemplo, cada persona puede tener un único pasaporte y cada pasaporte pertenece a una única persona.

Ejercicio: Se pide crear una consulta que muestre toda la información de las matrículas de los vehículos junto a su matrícula correspondiente.
Select *
from
    vehiculos v
join
    matriculas m
on 
    v.id = m.vehiculo_id

--Relaciones 1 a n
En una relación uno a muchos, un registro de una tabla puede estar relacionado con uno o varios registros de otra tabla. Por ejemplo, podriamos tener una tabla de empleados y otra de departamentos. Cada empleado pertenece a un único departamento, pero un departamento puede tener varios empleados.

Ejercicio: Se pide crear una consulta que muestre toda la información de los países junto a su continente correspondiente. Observa dentro de los resultados que un país pertenece a un único continente, pero un continente puede tener varios países.
Select
    p.pais_id, 
    p.nombre, 
    p.continente_id, 
    c.continente_id,
    c.nombre
from
    continentes c
join
    paises p
on
    c.continente_id = p.continente_id

--Relaciones n a n
En una relación muchos a muchos, un registro de una tabla puede estar relacionado con uno o varios registros de otra tabla, y viceversa.
Relaciones N a N
En bases de datos relaciones, con dos tablas solo podemos lograr relaciones de uno a uno o uno a muchos. Para lograr relaciones muchos a muchos, se necesita una tabla intermedia con ciertas características que estudiaremos más adelante.

Ejercicio resuelto
Se tiene un sistema que guarda información de profesores y alumnos. Cada profesor puede tener varios alumnos y cada alumno puede tener varios profesores. Para lograr esto se tiene una tabla profesores, una tabla alumnos y una tabla profesores_alumnos que relaciona a los profesores con los alumnos.

Tabla profesores

profesor_id	nombre
1	Ana
2	Pedro
3	Luis
Tabla alumnos

alumno_id	nombre
1	Marta
2	Elena
3	Juan
Tabla profesores_alumnos

profesor_id	alumno_id
1	1
1	2
2	1
Al unir las tablas profesores y profesores_alumnos con un JOIN, obtendríamos:

profesor_id	nombre	alumno_id
1	Ana	1
1	Ana	2
2	Pedro	1
Al unir las tabla anterior con la tabla alumnos, obtendríamos:

profesor_id	nombre	alumno_id	nombre
1	Ana	1	Marta
1	Ana	2	Elena
2	Pedro	1	Marta
Donde podemos ver que por cada registro de la tabla profesores hay uno o varios registros en la tabla profesores_alumnos, y por cada registro de la tabla profesores_alumnos hay un registro en la tabla alumnos.

Para hacer el join entre más de 2 tablas, se puede hacer un join por cada tabla que se quiera unir.

SELECT *
FROM profesores
JOIN profesores_alumnos
ON profesores.profesor_id = profesores_alumnos.profesor_id
JOIN alumnos
ON profesores_alumnos.alumno_id = alumnos.alumno_id;

Ejercicio:Se tiene una base de datos con un catálogo de objetos y colores. Cada objeto puede tener varios colores y cada color puede estar asociado a varios objetos.
Select *
from
    objetos o
join
    objetos_colores oc
on 
    o.objeto_id = oc.objeto_id
join
    colores c
on
    c.color_id = oc.color_id

--Características de la tabla intermedia
Ideas clave
En una relación muchos a muchos, un registro de una tabla puede estar relacionado con uno o varios registros de otra tabla, y viceversa.
Las bases de datos relacionales no permiten relaciones muchos a muchos directamente, por lo que se necesita una tabla intermedia que relacione a las tablas principales.
La tabla intermedia debe tener una columna que sea clave foránea de cada una de las tablas principales.
**Entendiendo la tabla intermedia**
Si tenemos dos tablas A y B que queremos relacionar de manera muchos a muchos, necesitamos una tabla intermedia C que relacione a ambas tablas.

Ejercicio: Agrega registros a la tabla profesores_alumnos para que Julia tenga a Elena como alumna y Pedro tenga a Juan como alumno. Luego muestra todas las columnas de las tres tablas.
Insert into profesores_alumnos values (1,2), (2,3);
Select *
from profesores p
join profesores_alumnos pa
on p.profesor_id = pa.profesor_id
join alumnos a
on a.alumno_id = pa.alumno_id

--Sin restricción de unicidad
Ideas clave
La tabla intermedia debe tener una columna que sea clave foránea de cada una de las tablas principales.
Si hay una restricción de unicidad, se garantiza que no haya registros duplicados en la tabla intermedia. Esto se logra con una clave primaria compuesta de las claves foráneas de las tablas principales.
Restricción de unicidad
Supongamos que tenemos un sistema de gestión para una biblioteca que incluye tres tablas principales: Libros, Usuarios y Pedidos. La tabla Pedidos actúa como una tabla intermedia para manejar la relación de muchos a muchos entre Libros y Usuarios. En este sistema, un libro puede ser solicitado por múltiples usuarios, y cada usuario puede solicitar varios libros.
Antes de construir la tabla intermedia tenemos que hacernos una pregunta importante: ¿un usuario puede pedir el mismo libro más de una vez?

Si la respuesta es sí, entonces no necesitamos una restricción de unicidad en la tabla intermedia.

Si la respuesta es no, entonces debemos asegurarnos de que no haya registros duplicados en la tabla intermedia y esto se hace con una restricción de unicidad.
En este ejemplo tiene sentido que un usuario pueda pedir el mismo libro más de una vez, por lo que nuestra tabla intermedia tendría la siguiente estructura:

libro_id	usuario_id
1	1
1	1
2	2
2	2
3	1
Dentro de la tabla podemos ver que el usuario 1 ha pedido el libro 1 dos veces, lo cual no es un problema si no hay una restricción de unicidad.

Ejercicio:
Selecciona a todos los usuarios que han pedido el mismo libro más de una vez. Las columnas a mostrar son usuario_id, libro_id y veces donde veceses el número de veces que el usuario ha pedido el libro.
Pista: Agrupa por libro_id y usuario_id y cuenta cuantos registros hay por cada grupo. Reflexiona si debes ocupar where o having para filtrar los resultados.
Select usuario_id, libro_id, count(libro_id) as veces
from pedidos
group by libro_id, usuario_id
having veces > 1;

--Con restricción de unicidad
Supongamos que tenemos un sistema que guarda información de proyectos y empleados. Cada empleado puede trabajar en varios proyectos y cada proyecto puede tener varios empleados trabajando en él. Para manejar esto, tenemos una tabla empleados, una tabla proyectos y una tabla empleados_proyectos que relaciona a los empleados con los proyectos.
Antes de construir la tabla intermedia tenemos que hacernos una pregunta importante: ¿un empleado puede estar en el mismo proeycto más de una vez?

Si la respuesta es sí, entonces no necesitamos una restricción de unicidad en la tabla intermedia.

Si la respuesta es no, entonces debemos asegurarnos de que no haya registros duplicados en la tabla intermedia y esto se hace con una restricción de unicidad.

En este caso, un empleado no puede estar asignado al mismo proyecto más de una vez. Para evitar que esto suceda, al crear la tabla agregaremos una clave primaria compuesta de las claves foráneas de las tablas principales.

CREATE TABLE Empleados_Proyectos (
empleado_id INT,
proyecto_id INT,
PRIMARY KEY (empleado_id, proyecto_id),
FOREIGN KEY (empleado_id) REFERENCES Empleados(id),
FOREIGN KEY (proyecto_id) REFERENCES Proyectos(id)
);
De esta manera, si se intenta agregar un registro con un empleado y un proyecto que ya existen en la tabla, se generará un error, lo que asegura que no haya registros duplicados en la tabla intermedia.

Ejercicio: Crea una consulta que seleccione todos los empleados junto con la cantidad de proyectos asignados a cada uno, demostrando que no hay registros duplicados en la tabla intermedia. Las columnas de la consulta deben ser nombre, puesto y cantidad_proyectos.
Select e.nombre, e.puesto, count(ep.proyecto_id) as cantidad_proyectos
from empleados_proyectos ep
join empleados e
on e.id = ep.empleado_id
group by e.id
order by cantidad_proyectos desc;

--Inner Join
**Ideas clave**
La cláusula JOIN nos permite combinar los datos de varias tablas en una única tabla de resultados.
Existen varios tipos de joins, cuando no se especifica el tipo de join se utiliza INNER JOIN.
La diferencia entre los tipos de joins radica en cómo se manejan los registros que no tienen una clave común en ambas tablas.
I**NNER JOIN es la opción por defecto**
Hasta el momento hemos utilizado INNER JOIN, pero no lo hemos especificado. Si no se especifica el tipo de JOIN, por defecto se utiliza INNER JOIN. Esto implica que:
SELECT * FROM usuarios JOIN datos_contacto ON usuarios.email = datos_contacto.email
Es lo mismo que:
SELECT * FROM usuarios INNER JOIN datos_contacto ON usuarios.email = datos_contacto.email
**¿Qué hace INNER JOIN?**
INNER JOIN combina los registros de ambas tablas en base a una condición de unión. Solo se incluyen los registros que tienen una clave común en ambas tablas. Si no hay una clave común, los registros no se incluyen en el resultado final.
**Ejercicio**
Une las tablas utilizando JOIN (o INNER JOIN) para obtener todos los registros de ambas tablas. Mira las tablas antes de realizar el ejercicio y pon especial atención en Francisco, quien no tiene ninguna nota en el sistema. francisco no aparece porque no tiene registro en notas asociado con el email de usuarios.
Select *
from
    usuarios u
inner join
    notas n
on u.email = n.email;

--Inner Join con diagrama de venn
**Ideas clave**
Los diagramas de Venn son una forma visual de representar conjuntos y operaciones entre ellos.
Podemos utilizar los diagramas de Venn para visualizar lo que sucede con los registros de las tablas al realizar distintos tipos de JOIN.
En el diagrama de Venn, un INNER JOIN se visualiza como la intersección entre dos círculos.
**Diagramas de Venn**
Un diagrama de Venn nos permite visualizar conjuntos y operaciones entre ellos. Estos diagramas se componen de círculos que representan conjuntos de datos. En ellos, los elementos que tienen en común los distintos conjuntos de datos se representan en la intersección de los círculos.
Cuando trabajamos con bases de datos, podemos utilizar estos diagramas para visualizar los registros que se obtendrían al realizar distintos tipos de JOIN. Especialmente, nos serán útiles para visualizar los resultados de una operación de join.
Para hacerlo, pondremos como elementos las claves de unión de las tablas. Los elementos que solo estén en la primera tabla los representaremos en un círculo, los elementos que solo estén en la segunda tabla los representaremos en otro círculo y los elementos que estén en ambas tablas los representaremos en la intersección de ambos círculos.
**Ejercicio**
En un papel, dibuja un diagrama de Venn que muestre los registros que se obtendrían al realizar un INNER JOIN entre las tablas actores y peliculas.
En el editor de código a continuación, realiza una consulta que muestre el nombre de los actores y los títulos de las películas en las que han actuado.
¿El resultado del código coincide con tu dibujo? SI
Select a.nombre, p.titulo
from
    actores a
inner join
    peliculas p
on
    a.actor_id = p.actor_id;

--Left Join
Al utilizar un LEFT JOIN, si en la tabla izquierda está la clave pero en la tabla derecha no, el registro de la tabla izquierda aparecerá en el resultado final con valores NULL en los campos de la tabla derecha.
Inner join V.S Left join
Para ilustrar la diferencia trabajaremos con las tablas usuarios y notas.
+-----------------------------+          +-----------------------------+
|         usuarios            |          |           notas             |
+-----------------------------+          +-----------------------------+
| email                       |----------| email                       |
| nombre                      |          | notas                       |
| edad                        |          |                             |
+-----------------------------+          +-----------------------------+
Tabla usuarios

email	nombre	edad
juan.perez@example.com	Juan Pérez	30
maria.gonzalez@example.com	Maria González	25
john.doe@example.com	John Doe	40
francisco@example.com	Test User	22
Tabla notas

email	notas
juan.perez@example.com	90
maria.gonzalez@example.com	100
john.doe@example.com	80
juan.perez@example.com	100
maria.gonzalez@example.com	100

Si hacemos un INNER JOIN entre las tablas usuarios y notas utilizando el campo email como clave, nos quedaremos con los siguientes registros:

SELECT u.email, u.nombre, u.edad, n.notas
FROM usuarios u
INNER JOIN notas n ON u.email = n.email;
email	nombre	edad	notas
juan.perez@example.com	Juan Pérez	30	90
juan.perez@example.com	Juan Pérez	30	100
maria.gonzalez@example.com	Maria González	25	100
maria.gonzalez@example.com	Maria González	25	100
john.doe@example.com	John Doe	40	80
Aquí veremos que francisco@example.com no aparece en el resultado final, ya que no tiene ninguna nota en el sistema. Sin embargo, si utilizamos un LEFT JOIN entre las tablas usuarios y notas, como el siguiente:

SELECT u.email, u.nombre, u.edad, n.notas
FROM usuarios u
LEFT JOIN notas n ON u.email = n.email;
Obtendremos los siguientes registros:

email	nombre	edad	notas
juan.perez@example.com	Juan Pérez	30	90
juan.perez@example.com	Juan Pérez	30	100
maria.gonzalez@example.com	Maria González	25	100
maria.gonzalez@example.com	Maria González	25	100
john.doe@example.com	John Doe	40	80
francisco@example.com	Test User	22	NULL
Al hacer un LEFT JOIN, el registro de francisco@example.com aparece en el resultado final con valores NULL en los campos de la tabla notas, puesto que Francisco no tiene ninguna nota en el sistema, sin embargo, está en la tabla de usuarios.

**Realizando un LEFT JOIN**
La sintaxis para utilizar LEFT JOIN es similar a INNER JOIN:
SELECT * FROM tabla1 LEFT JOIN tabla2 ON tabla1.atributo = tabla2.atributo

**Ejercicio**
Ejercicio
Se tiene una tabla llamada empleados y otra llamada *departamentos+. Utilizando lo aprendido, selecciona a todos los empleados junto a sus departamentos correspondientes, incluyendo a los empleados que aún no han sido asignados a ningún departamento. En ambas tablas existe la columna email.

+----------------------------+                   +----------------------------+
|          empleados         |                   |        departamentos       |
+----------------------------+                   +----------------------------+
| email                      |<----------------->| email                      |
| nombre                     |                   | departamento               |
| edad                       |                   +----------------------------+
+----------------------------+
select *
from
    empleados e
left join
    departamentos d
on e.email = d.email;

--Left Join en diagrama de venn
En el diagrama de Venn un LEFT JOIN se visualiza como el conjunto de la izquierda.
Cuando hacemos una operación de Left Join entre la tabla A y la tabla B, en los resultados aparecerán todos los registros de la tabla A, incluso aquellos que no tienen una clave correspondiente en la tabla B. O sea podemos visualizar el Left Join como el conjunto de la izquierda en un diagrama de Venn.

Analizando el resultado
Adicionalmente viendo el diagrama de Venn podemos observar el resultado de la operación de Left Join entre las tablas productos y ventas. En el podemos ver:

Los registros con id 1, 2 y 3 de la tabla productos aparecerán en los resultados del Left Join dado que los 3 están en el conjunto de la izquierda.
Los registros con id 4 de la tabla ventas no aparecerán en los resultados del Left Join dado que no están en el conjunto de la izquierda.
El registro 1 y 2 no aparecerán con datos de la tabla de venta ya que solo se encuentran en la tabla de la izquierda.
El registro 3 aparecerá con los datos de la tabla venta ya que es el único que se encuentra en ambas tablas.
**Ejercicio**
Dada las siguientes tablas:

Tabla Profesion

id	Profesion
1	Ingeniero
2	Médico
3	Abogado
4	Arquitecto
Tabla Personas

id	Nombre	profesion_id
1	Juan	1
2	Maria	2
3	Ana	3
Se tiene una tabla llamada Personas que contiene el nombre de las personas y el id de la profesión a la que pertenecen. Se quiere obtener un listado de todas las profesiones y las personas que pertenecen a cada una de ellas. Si una profesión no tiene personas asociadas, se debe mostrar el nombre de la profesión y NULL en el campo Nombre. La tabla resultante sólo debe contener dos columnas: Profesion y Nombre.
Select pr.profesion, pe.nombre
from
    profesion pr
left join
    personas pe
on
    pr.id = pe.profesion_id;

--Right join
Al utilizar RIGHT JOIN, si en la tabla derecha está la clave pero en la tabla izquierda no, el registro de la tabla derecha aparecerá en el resultado final con valores NULL en los campos de la tabla izquierda.
RIGHT JOIN
RIGHT JOIN funciona de la misma forma que LEFT JOIN, pero invierte el orden de las tablas. Mientras que el LEFT JOIN devuelve todas las filas de la tabla izquierda y las coincidencias correspondientes de la tabla derecha (rellenando con NULL si no hay coincidencia), el RIGHT JOIN hace lo contrario: devuelve todas las filas de la tabla derecha y las coincidencias correspondientes de la tabla izquierda.

Para hacer un RIGHT JOIN en SQL, se utiliza la siguiente sintaxis:

SELECT * 
FROM tabla1 
RIGHT JOIN tabla2 
ON tabla1.atributo = tabla2.atributo;

**Ejercicio**
Nos solicitan seleccionar todos los registros de los departamentos de todas las oficinas junto con sus correspondientes empleados, incluyendo aquellos departamentos que no tienen empleados asociados. Ambas tablas contienen la columna email.
Select *
from
    empleados e
right join
    departamentos d
on e.email = d.email

--Left Join y Right Join
**Ideas clave**
Al utilizar un LEFT JOIN, si en la tabla izquierda está la clave pero en la tabla derecha no, el registro de la tabla izquierda aparecerá en el resultado final con valores NULL en los campos de la tabla derecha.
Al utilizar un RIGHT JOIN, si en la tabla derecha está la clave pero en la tabla izquierda no, el registro de la tabla derecha aparecerá en el resultado final con valores NULL en los campos de la tabla izquierda.
LEFT JOIN y RIGHT JOIN son un reflejo el uno del otro. Utilizar LEFT JOIN o RIGHT JOIN depende simplemente de qué tabla quieres nombrar primero.
SELECT * 
FROM tabla1 
LEFT JOIN tabla2 ON tabla1.id = tabla2.id
Es prácticamente lo mismo que:

SELECT * 
FROM tabla2
RIGHT JOIN tabla1 on tabla2.id = tabla1.id
LEFT JOIN y RIGHT JOIN son un reflejo el uno del otro. Sin embargo, existe una pequeña diferencia cuando los utilizamos en conjunto con SELECT *, dado que los atributos de los primera tabla se mostrarán primero.
Para obtener los resultados en el mismo orden simplemente podemos especificar el orden que queremos.
SELECT usuarios.*, notas.* 
FROM Notas 
RIGHT JOIN Usuarios ON Notas.email = Usuarios.email;` 
Ejercicio
Selecciona todos los registros de todos los productos (tabla productos) junto a sus precios (tabla precios), incluyendo a los productos que no tienen precio asignado. Las tablas se relacionan entre si por la columna producto_id.
Select *
from
    productos p
left join
    precios pre
on p.producto_id = pre.producto_id;

--Identificando tipos de join
**Ideas clave:**
Existen múltiples tipos de JOIN
Hasta el momento hemos visto INNER JOIN, LEFT JOIN y RIGHT JOIN
Debemos saber identificar el tipo de JOIN a utilizar en una consulta a partir de la petición
**Guía útil para identificar el tipo de join**
1. INNER JOIN
Cuándo usarlo: Cuando necesitas sólo las filas donde haya coincidencias en ambas tablas.
Resultado: Devuelve solo las filas con datos correspondientes en ambas tablas.
2. LEFT JOIN (o LEFT OUTER JOIN)
Cuándo usarlo: Cuando necesitas todas las filas de la tabla de la izquierda y las filas coincidentes de la tabla de la derecha.
Resultado: Devuelve todas las filas de la tabla de la izquierda y las coincidencias de la tabla de la derecha, con NULLs donde no haya coincidencias.
3. RIGHT JOIN (o RIGHT OUTER JOIN)
Cuándo usarlo: Cuando necesitas todas las filas de la tabla de la derecha y las filas coincidentes de la tabla de la izquierda.
Resultado: Devuelve todas las filas de la tabla de la derecha y las coincidencias de la tabla de la izquierda, con NULLs donde no haya coincidencias.
**Ejercicio**
Se tiene una base de datos con dos tablas principales: autores y libros.
Crea una consulta con el fin de obtener información que muestre el nombre del autor junto con el título del libro que ha escrito. La consulta debe incluir sólo aquellos libros que tienen autores asignados
Las columnas de la consulta deben llamarse:
nombre_autor
titulo_libro
Select a.nombre as nombre_autor, l.titulo as titulo_libro 
from
    autores a
inner join
    libros l
on a.id = l.id_autor;

**Ejercicio**
Ejercicio
Se tiene una base de datos con dos tablas principales: empleados y proyectos.
Crea una consulta con el fin de obtener información detallada, y que muestre el nombre del empleado junto con el nombre del proyecto en el que participa.
La tabla de empleados tiene los siguientes campos:
id (identificador único del empleado)
nombre (nombre del empleado)
id_proyecto (identificador del proyecto en el que participa el empleado)
La tabla de proyectos tiene los siguientes campos:
id (identificador único del proyecto)
nombre_proyecto (nombre del proyecto)
El resultado debe tener las columnas con los siguientes nombres e incluir a todos los empleados, aunque no tengan asignado proyecto.
nombre_empleado
nombre_proyecto

Select e.nombre as nombre_empleado, p.nombre_proyecto
from
    empleados e
left join 
    proyectos p
on
    e.id_proyecto = p.id;

**Ejercicio**
Se tiene una base de datos con dos tablas principales: empleados y proyectos.
Se pide obtener una lista de todos los proyectos junto con los nombres de los empleados asignados a cada proyecto, incluyendo aquellos proyectos que no tienen empleados asignados", se utilizan las siguientes columnas:
El resultado debe estar únicamente compuesto por las columnas nombre_empleado y nombre_proyecto que corresponden al nombre del empleado y al nombre del proyecto de sus respectivas tablas, incluso aquellos que no tienen empleados asignados (empleado NULL).
Select e.nombre as nombre_empleado, p.nombre_proyecto
from
    empleados e
right join
    proyectos p
on
    e.id_proyecto = p.id;

--Full outer join
Al utilizar FULL OUTER JOIN, se obtienen todos los registros de ambas tablas, incluyendo los registros no coincidentes.
Full outer join
Full outer join es una combinación de LEFT JOIN y RIGHT JOIN. Devuelve todos los registros de ambas tablas, incluyendo los registros no coincidentes.
Implementando full outer join en SQLite
El motor de base de datos SQLite no soporta operaciones de FULL OUTER JOIN. Sin embargo, se puede lograr el mismo efecto con la siguiente sintaxis:
SELECT *
FROM tableA
LEFT JOIN tableB
  ON tableA.column_name = tableB.column_name
UNION
SELECT *
FROM tableA
RIGHT JOIN tableB
  ON tableA.column_name = tableB.column_name
**Ejercicio**
Dada las tablas empleados y departamentos que vimos previamente, escribe una consulta que devuelva todos los registros coincidentes y no coincidentes entre las tablas empleados y departamentos.
Select *
from empleados e
left join departamentos d
on e.id_departamento = d.id_departamento
union
Select *
from empleados e
right join departamentos d
on e.id_departamento = d.id_departamento;
**Union vs Union all**
Si miramos el diagrama detenidamente, nos daremos cuenta que si simplemente unimos los resultados de un LEFT JOIN y un RIGHT JOIN podríamos estar duplicando los registros en el punto de intersección. Aquí es donde debemos recordar la diferencia entre UNION y UNION ALL. UNION elimina los registros duplicados, mientras que UNION ALL no lo hace. Por lo tanto si unimos los resultados de un LEFT JOIN y un RIGHT JOIN debemos utilizar UNION para evitar duplicados.
SELECT *
FROM tableA
LEFT JOIN tableB
  ON tableA.column_name = tableB.column_name
UNION
SELECT *
FROM tableA
RIGHT JOIN tableB
  ON tableA.column_name = tableB.column_name
**Ejercicio**
Se tienen las tablas clientes y pedidos. Crea una consulta que devuelva todos los registros coincidentes y no coincidentes entre las tablas clientes y pedidos.
Select *
from clientes c
left join pedidos p
on c.id_cliente = p.id_cliente
union
select *
from clientes c
right join pedidos p
on c.id_cliente = p.id_cliente;

--Left excluding join
Un left excluding join es una combinación de left join con una cláusula WHERE para mostrar los registros de la tabla izquierda que no tienen coincidencias en la tabla derecha.
En este ejemplo si queremos obtener los alumnos que no tienen calificaciones, podemos hacerlo con un left excluding join.
SELECT *
FROM alumnos a
LEFT JOIN calificaciones c
ON a.id = c.alumno_id
*WHERE c.calificacion IS NULL;*
Y como resultado obtendríamos:
id	nombre	apellido	id	alumno_id	calificacion
3	Pedro	Lopez		
**Ejercicio**
Crea una consulta SQL que muestre todos los datos de las tablas mencionadas de las personas que no tienen profesión.
Select *
from
    personas pe
left join
    profesion pr
on pr.id = pe.profesion_id
where pe.profesion_id is null;

--Right excluding join
Un right excluding join es una combinación de right join con una cláusula WHERE para mostrar los registros de la tabla derecha que no tienen coincidencias en la tabla izquierda.
¿Cómo funciona un right excluding join?
Right excluding join es muy similar a Left excluding Join. Una consulta de right excluding join nos entrega los registros que no tienen coincidencia con la tabla izquierda.
SELECT *
FROM tableA
RIGHT JOIN tableB
  ON tableA.name = tableB.name
WHERE tableA.name IS NULL
Es importante observar que además de cambiar el JOIN hay que cambiar el WHERE y sustituir tableA por tableB
**Caso práctico**
En una base de datos se tienen las tablas de calificaciones y cursos.
Tabla: calificaciones
id	alumno_id	curso_id	calificacion
1	1	1	8.5
2	1	2	7.9
3	2	1	9.2
4	2	2	8.8
5	3	1	7.5
6	4	2	9.1
Tabla: cursos
id	nombre	docente_id
1	Matemáticas	1
2	Ciencias	1
3	Arte	1
Nos piden obtener toda la información de los cursos que no tienen calificaciones. Para resolver esto con un right excluding join, la consulta sería:
SELECT *
FROM calificaciones c
RIGHT JOIN cursos cu
ON cu.id = c.curso_id
WHERE c.id IS NULL;
Como resultado obtendríamos:

id	alumno_id	curso_id	calificacion	id	nombre	docente_id
3	Arte	1
**Ejercicio**
Utiliza un right join para obtener a los docentes que aún no tienen un curso asignado.
Select *
from cursos c
right join docentes d
on c.docente_id = d.id
where c.docente_id is null;

--Full outer excluding join
Full Outer Excluding Join es una combinación de FULL OUTER JOIN con una cláusula WHERE para mostrar los registros que no tienen coincidencias en ambas tablas.
Aunque SQLite no soporta directamente FULL OUTER JOIN, podemos realizar un FULL OUTER EXCLUDING JOIN utilizando una combinación de LEFT EXCLUDING JOIN y RIGHT EXCLUDING JOIN.
SELECT *
FROM tabla_A
LEFT JOIN tabla_B ON tabla_A.clave = tabla_B.clave
WHERE tabla_B.clave IS NULL
UNION
SELECT *
FROM tabla_A
RIGHT JOIN tabla_B ON tabla_A.clave = tabla_B.clave
WHERE tabla_A.clave IS NULL
Esta consulta nos dará todos los registros de tabla_A que no tienen coincidencia en tabla_B, y todos los registros de tabla_B que no tienen coincidencia en tabla_A.
**Ejercicio**
Consideremos las siguientes tablas:
Tabla empleados:
id_empleado	nombre	id_departamento
1	Ana	10
2	Juan	20
3	María	30
4	Carlos	NULL
Tabla departamentos:
id_departamento	departamento
10	Recursos Humanos
20	Finanzas
40	Marketing
Escribe una consulta SQL que implemente un FULL OUTER EXCLUDING JOIN entre las tablas empleados y departamentos. Esta consulta debe mostrar tanto a los Empleados que no están asignados a ningún departamento existente como a los departamentos que no tienen empleados asignados.
Una vez que hayas escrito la consulta, ejecútala y analiza los resultados y contesta las siguientes preguntas:
¿Por qué Carlos aparece en el resultado de la consulta? - porque no tiene departamento asociado
¿Por qué el departamento de Marketing aparece en el resultado? - porque no tiene empleado asociado
¿Cómo se diferencia este resultado de un FULL OUTER JOIN regular? que muestra todos los no relacionados de ambas tablas, incluso cuando una las tablas no tenga un registro creado pero si asociado.
Select e.id_empleado, e.nombre, e.id_departamento, d.departamento
from empleados e
left join departamentos d
on e.id_departamento = d.id_departamento
where d.id_departamento is null
union
select e.id_empleado, e.nombre, d.id_departamento, d.departamento
from empleados e
right join departamentos d
on e.id_departamento = d.id_departamento
where e.id_empleado is null;
Nota: para evitar columnas duplicadas debemos especificarlo en el Select, pero debemos fijarnos en el left mostremos el id de la tabla izquiera y en el right el id de la tabla derecha

--Natural Join
**Ideas clave**
Natural Join es un JOIN que adicionalmente asume que las columnas con el mismo nombre son las columnas de unión.
La ventaja de usar NATURAL JOIN es que simplifica la escritura de la consulta, pero solo es útil cuando las columnas de unión tienen el mismo nombre y tipo de datos en ambas tablas.
**Natural Join**
El NATURAL JOIN es un tipo de join en SQL que se utiliza para combinar dos tablas utilizando las columnas que tienen el mismo nombre. Su sintaxis es la siguiente:
SELECT columnas
FROM tabla1
NATURAL JOIN tabla2;
Como se espera que las columnas se llamen igual no es necesario especificarla
**Veamos un ejemplo:**
Supongamos que tenemos dos tablas, clientes y pedidos, con la siguiente estructura:
Tabla clientes
id_cliente	nombre	email
1	Juan	juan@example.com
2	María	maria@example.com
3	Pedro	pedro@example.com
Tabla pedidos
id_pedido	id_cliente	producto	cantidad
1	1	Laptop	1
2	2	Tablet	2
3	1	Smartphone	1
En este caso, ambas tablas tienen una columna id_cliente por lo que, al hacer un Natural Join, esta será la columna utilizada para unir las tablas.
SELECT *
FROM clientes
NATURAL JOIN pedidos;
id_cliente	nombre	email	id_pedido	producto	cantidad
1	Juan	juan@example.com	1	Laptop	1
2	María	maria@example.com	2	Tablet	2
3	Pedro	pedro@example.com	3	Smartphone	1

**Ejercicio**
Realiza una consulta utilizando NATURAL JOIN que muestre el nombre del producto, la cantidad vendida y la fecha de venta de cada producto vendido. Utiliza los nombres originales de las columnas.
Select p.nombre, v.cantidad,v.fecha
from productos p
natural join ventas v;

--Natural Left Join
Así como para JOIN la opción de INNER es implícita, para NATURAL JOIN la opción de INNER también es implícita. y uno puede utilizar NATURAL INNER JOIN, NATURAL LEFT JOIN, NATURAL RIGHT JOIN.
Natural Left Join
NATURAL LEFT JOIN realiza un LEFT JOIN utilizando las columnas que tienen el mismo nombre en ambas tablas. Su uso es el siguiente:
SELECT columnas
FROM tabla1
NATURAL LEFT JOIN tabla2;
**Ejercicio**
Dada las tablas entregadas, crea un reporte que seleccione a todos los estudiantes (sus nombres), cursos inscritos y fecha de inscripciones. Si un estudiante no tiene cursos inscritos, se debe mostrar el estudiante sin los datos de inscripción.
Select e.nombre, i.curso, i.fecha
from estudiantes e
natural left join inscripciones i

--Self Join
**Ideas clave**
Un self join se utiliza para combinar una tabla consigo misma.
Self join no es un tipo de join específico, es solo el término utilizado para referirse a la operación de combinar una tabla consigo misma.
Para lograr el self join se utilizan otros tipos de joins como INNER JOIN o LEFT JOIN.
**Self Join**
Un self join es un tipo de join en SQL que se utiliza para combinar una tabla consigo misma. Se usa cuando queremos relacionar filas de una tabla con otras filas de la misma tabla. Aunque el término "self join" se refiere a esta relación consigo misma, en la práctica se utilizan otros tipos de joins como INNER JOIN o LEFT JOIN para realizar esta operación.
**Ejemplo**
Supongamos que tenemos una tabla llamada empleados con la siguiente estructura:
id_empleado	nombre	id_supervisor
1	Juan	NULL
2	María	1
3	Pedro	1
4	Ana	2
5	Luis	2
**Uso del Self Join**
Para obtener el nombre de todos los empleados junto con el nombre de su supervisor, podemos usar un self join de la siguiente manera:
SELECT e1.nombre AS nombre_empleado, e2.nombre AS nombre_supervisor
FROM empleados e1
LEFT JOIN empleados e2 ON e1.id_supervisor = e2.id_empleado;
nombre_empleado	nombre_supervisor
Juan	NULL
María	Juan
Pedro	Juan
Ana	María
Luis	María
En este ejemplo, estamos obteniendo el nombre de cada empleado (e1.nombre) y el nombre de su supervisor (e2.nombre). El self join se realiza uniendo la tabla empleados consigo misma (empleados e1 LEFT JOIN empleados e2), utilizando la columna id_supervisor de e1 y la columna id_empleado de e2.
Se utiliza LEFT JOIN en lugar de INNER JOIN para incluir a los empleados que no tienen supervisor, dado que en la consulta nos pedían los nombres de todos los empleados.
**Ejercicio**
Ejercicio
Se tiene la tabla clientes con los siguientes datos:
id_cliente	nombre	id_cliente_referente
1	Juan	NULL
2	María	1
3	Pedro	1
4	Ana	2
5	Luis	2
Utilizando lo aprendido escribe una consulta que muestre el nombre de todos los clientes junto con el nombre de su cliente referente. Las columnas de la tabla resultante deben llamarse nombre_cliente y nombre_cliente_referente.
Select c1.nombre as nombre_cliente, c2.nombre as nombre_cliente_referente
from clientes c1
left join clientes c2
on c1.id_cliente_referente = c2.id_cliente -- en el on cada tabla relaciona al id contrario

**Ejercicio**
Se tiene la tabla amigos que contiene la información de amigos conectados en una red social.

id_amigo	nombre	id_amigo_conectado
1	Carlos	NULL
2	Laura	1
3	Miguel	1
4	Ana	2
5	Luis	3
Escribe una consulta SQL utilizando self join para obtener el nombre de cada amigo junto con el nombre de su amigo conectado. Solo se deben mostrar los amigos que tienen un amigo conectado. Las columnas de la tabla resultante deben llamarse nombre y nombre_amigo_conectado.
Tip 1: ¿Qué tipo de join utilizarías para obtener los amigos conectados? Inner Join para mostrra solo los que tienen amigos conectaos
Tip 2: ¿Qué columnas de la tabla amigos se relacionan entre sí? ID_Amigo y el ID_amigo_conectado
Select a1.nombre, a2.nombre as nombre_amigo_conectado
from amigos a1
inner join amigos a2
on a1.id_amigo_conectado = a2.id_amigo;

--Cross Join
Ideas clave
Cross Join genera el producto cartesiano de dos tablas.
Cross Join no requiere una condición de unión, dado que combina todas las filas de la primera tabla con todas las filas de la segunda tabla.
**Cross Join**
El Cross Join, también conocido como producto cartesiano, combina cada fila de la primera tabla con cada fila de la segunda tabla, generando un conjunto de resultados que es el producto cartesiano de ambas tablas. Es útil cuando se desea combinar todas las filas de una tabla con todas las filas de otra tabla.
La sintaxis de Cross Join es la siguiente:
SELECT columnas
FROM tabla1
CROSS JOIN tabla2;
**Ejemplo**
Se tienen dos tablas, una de figuras geométricas y otra de colores. Se pide generar una consulta que muestre todas las figuras junto con todos los colores posibles.
SELECT nombre_figura, nombre_color
from figuras
cross join colores;
**Ejercicio**
Se tiene una tabla de números y una tabla de pintas. Genera una consulta que muestre todos los números junto con todas las pintas posibles simulando una baraja de cartas ordenada por números de menor a mayor y las pintas en orden alfabético. Las columnas de la tabla resultante deben llamarse numero y pinta.
Select *
from numeros
cross join pintas
order by numero, pinta

--Join con funciones agregadas
Ideas clave:
Combinar datos de varias tablas: La claúsula JOIN nos permite combinar filas de dos o más tablas.
Escoger el tipo de JOIN: La elección del tipo de JOIN depende de si queremos contabilizar filas sin coincidencias, si es así usamos LEFT JOIN, si no usamos INNER JOIN.
Agrupar datos: El uso de GROUP BY permite agrupar filas que tienen el mismo valor en una o más columnas.
Funciones de agregación: Sobre datos agrupados, podemos utilizar funciones de agregación como SUM(), COUNT(), y AVG().
¿Qué vamos a aprender?
En estos ejercicios trabajaremos con problemas muy comunes en el análisis de datos, donde necesitamos combinar datos de varias tablas, agruparlos y obtener información de datos agregados. A lo largo de estos de estos ejercicios aprenderemos a identificar qué tipo de JOIN utilizar para cada caso.
Escenario
Se tiene una tabla de productos y una tabla de ventas. Se desea hacer una consulta que muestre cada producto con la cantidad de ventas realizadas.

tabla productos

id	nombre
1	smartphone
2	tablet
3	camiseta
4	zapatos
tabla ventas Para este primer ejercicio vamos a suponer que la tabla de ventas solo permite registrar la venta de un producto a la vez.

id	id_producto
1	1
2	1
2	2
2	2
3	1
Si miramos las ventas, veremos que el producto con id=1 se vendió 3 veces y el producto con id=2 se vendió 2 veces, y el resto no se ha vendido. Es decir, el resultado esperado sería:

nombre	cantidad_ventas
smartphone	3
tablet	2
camiseta	0
zapatos	0
La solución a este ejercicio requiere de unir las tablas productos y ventas y agrupar los resultados por el nombre del producto para finalmente contar el número de ventas realizadas.

La pregunta importante es: ¿Qué tipo de JOIN deberíamos usar para este caso? ¿INNER JOIN o LEFT JOIN?

Para tomar la decisión, debemos considerar si queremos contabilizar productos sin ventas. Si queremos contabilizarlos, entonces deberíamos usar un LEFT JOIN. Si no queremos contabilizarlos, entonces deberíamos usar un INNER JOIN. En el ejemplo, la tabla del reporte muestra camisetas y zapatos que no han sido vendidos, por lo que deberíamos usar un LEFT JOIN.

SELECT p.nombre, COUNT(v.id) AS cantidad_ventas
FROM productos p
LEFT JOIN ventas v ON p.id = v.id_producto
GROUP BY p.nombre;
**Ejercicio**
Se tiene una tabla de usuarios y una tabla de notas. Se desea hacer una consulta que muestre la cantidad de notas que ha registrado cada usuario incluso si no ha registrado ninguna nota. Las columnas de las tablas deben ser email y cantidad_notas. Ordena el resultado por la cantidad de notas.
Select u.email, count(n.notas) as cantidad_notas
from usuarios u
left join notas n
on u.email = n.email
group by u.email;

--Normalizacion
--Introduccion a la primer forma normal
Ideas clave
La redundancia en una base de datos puede llevar a inconsistencias a la hora de identificar el dato correcto, actualizarlo o eliminarlo.
La normalización es un proceso de eliminación de redundancia en una base de datos
¿Qué es la normalización?
La normalización es un proceso de eliminación de redundancia en una base de datos con el objetivo de prevenir inconsistencias. Este proceso consta de diversas etapas que, al aplicarse correctamente, permiten que la base de datos sea más eficiente y fácil de mantener. Cada una de estas etapas recibe el nombre de formas normales.
Primera forma normal
En la primera forma normal se busca que cada registro sea identificable de forma única en una tabla. Veamos un ejemplo de una tabla que no cumple con la primera forma normal:
Nombre	Apellido	Estado_Civil
Juan	Pérez	Casado
María	González	Soltera
Pedro	Rodríguez	Casado
Juan	Pérez	Soltero
María	González	Viuda
Pedro	Rodríguez	Soltero
Juan	Pérez	Divorciado
María	González	Soltera
Pedro	Rodríguez	Casado
En esta tabla, si preguntamos por el estado civil de Juan Pérez, no sabríamos cuál es el correcto. Además, ¿es Juan Pérez una persona o son personas distintas y es una coincidencia que tengan el mismo nombre y apellido? Este es uno de los problemas que se busca solucionar con la primera forma normal.

Ejercicio
Supongamos que cada combinación de nombre y apellido de la tabla personas, previamente descrita, corresponde a una única persona, y el último registro de cada persona es el correcto. Borra los registros incorrectos.
Hay formas de hacerlo automático, pero puedes simplemente borrar los registros incorrectos uno por uno separando las consultas con punto y coma. Es un trabajo tedioso, pero no olvidarás la lección de tener tablas que cumplan con la primera forma normal.
--Esta es una manera asumiendo que se tienen IDs incrementales, si no se puede hacer uno por uno
DELETE from personas
where ID not in (
    select max(ID)
    from personas
    group by nombre, apellido);
)

--Convirtiendo a 1fn
En primera forma normal debería haber una clave primaria que identifique de forma única a cada registro en una tabla.
En esta ocasión vamos a suponer que cada combinación de nombre y apellido de la tabla personas corresponde a una única persona, por lo tanto Juan Pérez con estado civil Casado, Soltero y Divorciado, son personas distintas. Para convertir esta tabla en primera forma normal agregaremos una clave primaria que identifique de forma única a cada registro.
En SQLite no podemos agregar una clave primaria a una tabla que ya tiene datos, por lo que tendremos que crear una nueva tabla con la clave primaria y luego insertar los datos de la tabla original en la nueva tabla.

CREATE TABLE personas2 (
  id INTEGER PRIMARY KEY AUTOINCREMENT,
  nombre TEXT NOT NULL,
  apellido TEXT NOT NULL,
  estado_civil TEXT NOT NULL
);

/* Seleccionamos los datos de personas y lo insertamos en personas2 */
INSERT INTO personas2 (nombre, apellido, estado_civil)
SELECT nombre, apellido, estado_civil
FROM personas;

/* Borramos la tabla personas */
DROP TABLE personas;

/* Renombramos la tabla personas2 a personas */
ALTER TABLE personas2 RENAME TO personas;

Ejercicio:
Create table productos2 (
    id integer primary key autoincrement,
    producto text not null,
    Categoría text not null,
    precio double not null
);

Insert into productos2 (producto, Categoría, precio)
select producto, Categoría, precio from productos;

Drop table productos;

Alter table productos2 rename to productos;

--Grupos repetitivos
Los grupos repetitivos son un indicio de que una tabla no cumple con la primera forma normal.
¿Qué son los grupos repetitivos?
Los grupos repetitivos son un conjunto de campos que se repiten en una tabla. Por ejemplo, si en una tabla se tienen los campos telefono1, telefono2 y telefono3, se podría considerar que estos campos forman un grupo repetitivo.
id	nombre	apellido	telefono1	telefono2	telefono3
1	Juan	Pérez	12345678	87654321	45678912
2	María	González	23456789	98765432	56789123
Existen diversos problemas asociados a los grupos repetitivos, pero en este caso puntual hay dos problemas evidentes:
¿Qué pasa si una persona tiene más de tres teléfonos? ¿Se debería modificar la tabla para agregar un campo telefono4, telefono5, etc.? ¿Cuál es el límite?
Si la mayoría de las personas solo tienen un teléfono, ¿cómo se maneja la información de los campos telefono2 y telefono3? ¿Se dejan en blanco? ¿Se les asigna un valor nulo? Probablemente terminemos con una gran cantidad de campos vacíos.
Resolver el problema de los grupos repetitivos es un paso importante en la normalización de una base de datos. En este caso, se podría crear una tabla telefonos que contenga los teléfonos de las personas y se relacione con la tabla personas, quedando de la siguiente manera:

tabla personas:
id	nombre	apellido
1	Juan	Pérez
2	María	González
tabla telefonos:
id_persona	telefono
1	12345678
1	87654321
1	45678912
2	23456789
2	98765432
2	56789123
De esta forma, se puede tener un número arbitrario de teléfonos por persona sin necesidad de modificar la estructura de la tabla personas.

Ejercicio
Dada la siguiente tabla, identifica los grupos repetitivos y propón una solución para normalizar la tabla.
tabla empleados_desnormalizado:
id	nombre	apellido	proyecto1	proyecto2	proyecto3
1	Juan	Pérez	1	2	3
Identifica los grupos repetitivos en la tabla empleados_desnormalizado. - El grupo repetitivo es el Proyecto
Crea las tablas empleados y proyectos en primera forma normal para que permitan almacenar la información de los empleados y los proyectos en una estructura normalizada.
Ingresa la información de la tabla empleados_desnormalizado en las tablas normalizadas.
Crea una consulta que muestre la información de los empleados y los proyectos a los que están asignados en una estructura normalizada. Las columnas de la consulta deben ser nombre, apellido y id_proyecto.

Create table empleados(
    id integer primary key autoincrement,
    nombre text not null,
    apellido text not null
);

Create table proyectos(
    id_proyecto integer primary key,
    id_empleado integer,
    foreign key (id_empleado) references empleados (id)
);

Insert into empleados (nombre, apellido) values ('juan', 'Pérez');
Insert into proyectos (id_proyecto, id_empleado) values (1,1),(2,1),(3,1);

Select e.nombre, e.apellido, p.id_proyecto
from
    empleados e
join
    proyectos p
on 
    e.id = p.id_empleado;

--Grupos repetitivos parte 2
Hay más de un forma de tener grupos repetitivos en una tabla
Grupos repetitivos
Los grupos repetitivos son conjuntos de campos que se repiten dentro de una tabla. Sin embargo, existen varias formas en las que estos grupos repetitivos pueden aparecer en una tabla.

En el ejercicio anterior, se vio un ejemplo de un grupo repetitivo en el que se tenían varios campos con el mismo propósito. Por ejemplo: telefono1, telefono2 y telefono3. Otra forma de tener grupos repetitivos es tener varios valores en un solo campo.
Los problema asociados a esta forma de grupos repetitivos son distintos:

Al modificar el teléfono de una persona, se debe modificar el campo Teléfonos que tiene varios registros y podríamos olvidar alguno o pasarlo a llevar por error.
Al tener los datos almacenados de esta forma es difícil saber cuanto teléfonos tiene cada persona.
Los teléfonos podrían tener distintos formatos y sería difícil establecer una restricción para que todos los teléfonos tengan el mismo formato.
Verificar si el mismo teléfono está asignado a más de una persona también se vuelve más complicado que al tener la tabla desnormalizada.

Por ejemplo, si quisiéramos contar la cantidad de teléfonos de cada persona, podríamos contar la cantidad de símbolos + en el campo Teléfonos. Para lograr esto, podemos calcular la cantidad total de caracteres en el campo Teléfonos y restarle la cantidad de caracteres en el mismo campo después de eliminar los símbolos +.

SELECT
    id,
    nombre_completo,
    LENGTH(telefonos) - LENGTH(REPLACE(telefonos, '+', '')) AS cantidad_telefonos
FROM
    personas;
Como se observa, es mucho más complicado que simplemente contar los registros de una tabla.

Ejercicio

El ejercicio se soluciona de la siguiente manera porque no productos en blanco, pero si hubieran, el error estaria incorrecto, porque estoy sumando 1 al final del conteo
Select
id, productos, length(productos) - length(replace(productos, ' ', '')) +1
from pedidos;

Si se quiere validar cuando el campo productos haya algun campo en blanco se podria utilizar el siguiente query.
SELECT
    id,
    productos,
    CASE 
        WHEN LENGTH(productos) = 0 THEN 0
        ELSE LENGTH(productos) - LENGTH(REPLACE(productos, ' ', '')) + 1 
    END AS cantidad_productos
FROM
    pedidos;

--Grupos repetitivos parte 3
Ideas clave
Los grupos repetitivos son un indicio de que una tabla no cumple con la primera forma normal.
Hay grupos repetitivos cuando tienes columnas que se repiten en una tabla, ejemplo: proyecto1, proyecto2, proyecto3.
Hay grupos repetitivos cuando tienes dentro de un campo varios valores, ejemplo: producto1 producto2 producto3.
Finalmente hay grupos repetitivos cuando tienes columnas que se repiten en varias filas, ejemplo: nombre, `apellido
Veamos por ejemplo la tabla departamentos:

nombre_departamento	nombre_persona	apellido_persona
Recursos Humanos	Ana	Pérez
TI	Pedro	Rodríguez
TI	Luis	González
Marketing	Marta	López
Marketing	Elena	Pérez
La tabla anterior, aunque sea un poco más difícil de evidenciar, también tiene grupos repetitivos. Dado que nombre_departamento se repite en varias filas para distintas personas.

Por ejemplo esto mismo podría estar como

nombre_departamento	personas
Recursos Humanos	Ana Pérez
TI	Pedro Rodríguez, Luis González
Marketing	Marta López, Elena Pérez
O como:

nombre_departamento	Nombre Persona 1	Nombre Persona 2	Nombre Persona 3
Recursos Humanos	Ana		
TI	Pedro	Luis	
Marketing	Marta	Elena	
Los problemas asociados a los grupos repetitivos son similares a los que se presentan en los ejercicios anteriores.

Si se quiere cambiar el nombre de un departamento, se tendría que cambiar en todas las filas que correspondan a ese departamento.
O un departamento podría quedar escrito de distintas formas, por ejemplo, TI, T.I., Tecnologías de la Información, etc. Y sería difícil establecer una restricción para que todos los departamentos tengan el mismo nombre.
Para eliminar los grupos repetitivos de esta tabla, se debe crear una nueva tabla Personas separada de la tabla Departamentos que relacione a las personas con los departamentos.
Deparamentos:

id	nombre_departamento
1	Recursos Humanos
2	TI
3	Marketing
Personas:

id	nombre_persona	apellido_persona	departamento_id
1	Ana	Pérez	1
2	Pedro	Rodríguez	2
3	Luis	González	2
4	Marta	López	3
5	Elena	Pérez	3

Ejercicio Didáctico
Con las tablas normalizadas de Departamentos y Personas. Muestra todos los departamentos y cuántas personas trabajan en cada uno. Las columnas deben llamarse nombre_departamento y cantidad_personas.
Select d.nombre_departamento, count(p.id) as cantidad_personas
from departamentos d
join personas p
on d.id = p.departamento_id
group by nombre_departamento;

--Dependencias parciales
En primera forma normal debe haber una clave primaria que identifique de forma única a cada registro en una tabla.
En la segunda forma normal no deben existir dependencias parciales, o sea que cada columna de una tabla debe depender de la clave primaria completa y no de una parte de ella.
Dependencias parciales
Una dependencia parcial es cuando una columna depende de solo una parte de la clave primaria. Esto por supuesto solo aplica a tablas con claves primarias compuestas.

Veamos un ejemplo de una tabla que tiene dependencias parciales:

Se tiene un sistema de registro de notas guardado en una única tabla llamada notas. La tabla tiene la siguiente estructura:

ID de estudiante	ID de curso	Nombre del estudiante	Nombre del curso	Nota
1	101	Juan	Matemáticas	90
1	102	Juan	Historia	85
2	101	Ana	Matemáticas	95
2	103	Ana	Ciencia	88
En esta tabla tenemos una clave primaria compuesta por ID de estudiante e ID de curso. Esta combinación de columnas identifica de manera única a cada fila, dado que para este ejemplo un estudiante no puede estar en el mismo curso dos veces.

Las dependencias parciales ocurren cuando un campo como Nombre del estudiante depende solo de ID de estudiante y no de ID de curso. O sea depende de una parte de la clave primaria y no de la clave primaria completa, por lo mismo se le llama dependencia parcial.

¿Por qué es importante eliminar las dependencias parciales?
Las dependencias parciales pueden causar problemas de actualización y eliminación de datos. Por ejemplo, si se cambia el nombre de un estudiante, se tendría que cambiar en todas las filas que correspondan a ese estudiante. O si se elimina un estudiante, se perdería la información de las notas asociadas a ese estudiante.

Eliminar dependencias parciales
Para llegar a la segunda forma normal (2FN) se deben identificar todas las dependencias parciales.

Nombre del estudiante es parcial porque depende solo de ID de estudiante y no de la clave primaria completa
Nombre del curso es parcial porque depende solo de ID de curso y no de la clave primaria completa
El siguiente paso es separar la tabla notas en dos tablas adicionales, una para los estudiantes y otra para los cursos.

Tabla estudiantes

ID_de_estudiante	Nombre_del_estudiante
1	Juan
2	Ana
Tabla cursos

ID_de_curso	Nombre_del_curso
101	Matemáticas
102	Historia
103	Ciencia
Luego se debe eliminar las columnas Nombre del estudiante y Nombre del curso de la tabla notas y agregar las claves foráneas ID de estudiante e ID de curso respectivamente.

Tabla notas

ID_de_estudiante	ID_de_curso	Nota
1	101	90
1	102	85
2	101	95
2	103	88

Ejercicio:
Dada las tablas estudiantes y cursos y notas normalizadas, crea una consulta que muestre el nombre del estudiante, el nombre del curso y la nota que ha obtenido cada estudiante en cada curso. Las columnas deben llamarse nombre_estudiante, nombre_curso y nota.

Select e.nombre_del_estudiante, c.nombre_del_curso, n.nota
from estudiantes e
join notas n
on e.id_de_estudiante = n.id_de_estudiante
join cursos c
on n.id_de_curso = c.id_de_curso;

--Dependencias transitivas
En la tercera forma normal no deben existir dependencias transitivas, o sea que una columna no puede depender de otra que no sea la clave primaria.
Qué son las dependencias transitivas
Las dependencias transitivas ocurren cuando una columna depende de otra que no es la clave primaria de manera indirecta. En otras palabras, si la columna A depende de la columna B y la columna B depende de la clave primaria, entonces la columna A depende transitivamente de la clave primaria.

Por ejemplo, consideremos la siguiente tabla Peliculas:

ID	Película	Director	Nacionalidad Director
1	El Padrino	Francis Ford Coppola	Estadounidense
2	Ciudad de Dios	Fernando Meirelles	Brasileño
3	El Laberinto del Fauno	Guillermo del Toro	Mexicano
4	Amélie	Jean-Pierre Jeunet	Francés
5	Parasite	Bong Joon-ho	Coreano
En esta tabla, podemos observar que la columna Nacionalidad Director depende de la columna Director. A su vez, Director depende de la clave primaria ID. Por lo tanto, Nacionalidad Director depende indirectamente de la clave primaria ID, lo que implica que hay una dependencia transitiva.

Este tipo de dependencia es importante de identificar y gestionar, especialmente en el contexto de la normalización de bases de datos, para evitar redundancias y asegurar la integridad de los datos.

Ejercicio
A partir de la siguiente tabla en 2FN, llamada Músicos, identifica las dependencias transitivas y propón una solución para normalizarla:

id	pais	musico	edad_musico
1	Estados Unidos	Beyoncé	42
2	Brasil	Gilberto Gil	81
3	Francia	David Guetta	56
4	India	A. R. Rahman	57
5	Corea del Sur	RM	29
Las tablas normalizadas deberían ser paises y musicos . Manten los nombres de los músicos y países en la tabla musicos. La claves primarias deben llamarse id en ambas tablas y la clave foránea debe llamarse pais_id.

Ingresa los datos normalizados en las tablas correspondientes.

