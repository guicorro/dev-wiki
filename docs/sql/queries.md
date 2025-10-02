# Cheatsheet de SQL

## Categorías

| Categoría | Nombre                       | Descripción                                            |
| --------- | ---------------------------- | ------------------------------------------------------ |
| DDL       | Data Definition Language     | Comandos que definen la estructura de la base de datos |
| DML       | Data Manipulation Language   | Comandos para manipular datos                          |
| DQL       | Data Query Language          | Comandos para consultar datos                          |
| DCL       | Data Control Language        | Comandos para acceso y permisos                        |
| TCL       | Transaction Control Language | Comandos para gestión de transacciones                 |

## Objetos de Base de Datos

- **TABLE**: Almacena datos en filas y columnas
- **VIEW**: Tabla virtual basada en una consulta SQL
- **SYNONYM**: Nombre alternativo para un objeto
- **SEQUENCE**: Genera valores numéricos
- **INDEX**: Mejora el rendimiento de las consultas
- **TRIGGER**: Automatiza acciones en eventos de la tabla

## Palabras clave de SQL

- **TYPE**: Palabras clave importantes
- **GROUP BY, HAVING**: Palabras clave de agregación

---

## Comandos SQL por Categoría

### DDL (Definición de datos)

```sql
CREATE TABLE Students(
	id INT PRIMARY KEY,
	name VARCHAR(100),
	age INT
);

ALTER TABLE Students ADD email VARCHAR(200);
ALTER TABLE Students MODIFY email VARCHAR(200);
ALTER TABLE Students DROP COLUMN email;
DROP TABLE Students;
```

### DQL (Consulta de datos)

```sql
SELECT * FROM Students;
SELECT * FROM Students WHERE rollno=234;
SELECT name, grade FROM Students ORDER BY gender;
SELECT * FROM Students INNER JOIN Section ON Students.section_id = Section.id;
```

### DML (Manipulación de datos)

```sql
INSERT INTO Students(rollno, name, age) VALUES (234, 'Oneto', 21);
UPDATE Students SET age=22 WHERE rollno=234;
DELETE FROM Students WHERE rollno=234;
SELECT avg(age) AS AverageAge FROM Students GROUP BY section;
```

### DCL (Control de acceso)

```sql
GRANT, REVOKE
```

### TCL (Control de transacciones)

```sql
COMMIT, ROLLBACK, SAVEPOINT, SET TRANSACTION
```

---

## Operaciones de Conjuntos

- **UNION**: Combina resultados, elimina duplicados
- **UNION ALL**: Combina resultados, mantiene duplicados
- **INTERSECT**: Devuelve filas comunes
- **EXCEPT/MINUS**: Devuelve filas de la primera consulta que no están en la segunda

## Joins

- **INNER JOIN**: Devuelve filas coincidentes de ambas tablas
- **LEFT OUTER JOIN**: Devuelve todas las filas de la izquierda y las coincidentes de la derecha
- **RIGHT OUTER JOIN**: Devuelve todas las filas de la derecha y las coincidentes de la izquierda
- **FULL OUTER JOIN**: Devuelve todas las filas cuando hay coincidencia en una u otra tabla

## Funciones de Agregación

- **AVG**: Promedio
- **MIN**: Valor mínimo
- **MAX**: Valor máximo
- **COUNT**: Cuenta total
- **SUM**: Suma total

## Restricciones

- **PRIMARY KEY**: Clave primaria
- **FOREIGN KEY**: Clave foránea
- **UNIQUE**: Valor único
- **CHECK**: Condición
- **DEFAULT**: Valor por defecto
- **NOT NULL**: No puede ser nulo

## Operadores SQL

- **Aritméticos**: +, -, \*, /
- **Bitwise**: &, |
- **Lógicos**: AND, OR, NOT
- **Comparación**: =, <>, >, <, >=, <=

---
