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

--
