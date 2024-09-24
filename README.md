# Proyecto Final: Score Crediticio para Empresas
Integrantes : Bremel Arones Rea / Patricia Lahura Albujar

Docente: Ing. Kevin Rivera Vergaray

## **1. Iniciación: Definición de los objetivos y el alcance inicial de la base de datos.**
### Título del Problema:  Score Crediticio para Empresas
### **Descripción del Problema:**
En los últimos años, una entidad bancaria ha experimentado un aumento significativo en la tasa de morosidad de sus préstamos comerciales. Este incremento ha generado pérdidas financieras y ha afectado negativamente la liquidez del banco. Se requiere asignar un score crediticio para fortalecer el proceso de evaluación de préstamos.

### **Tablas y Campos Relevantes:**

- Empresas: Se registran los datos de identificación de las empresas y sector al cual perteneces.
- Sector: Se registra la equivalencia del código SIC con el sector económico al cual se hace referencia. 
- Ejecutivo de relación: Se registran los datos del ejecutivo de la entidad a cargo de la cuenta. 
- Préstamos: Se registran los datos asociados al préstamo como monto, fecha de vencimiento, entre otros. 
- Garantías: Se registran los datos de las garantías que cubren el préstamo.
- Historial Crediticio Externo: Se registran los datos del desempeño de la empresa en el Sistema Financiero. 
- Estados Financieros: Se registran los datos principales de la situación financiera de la empresa. 
- Score: Se registran los datos del cálculo del Score con el nivel de riesgo de la empresa. 
- Cuotas: Se registran los datos de las cuotas que tiene que pagar el cliente por sus obligaciones contraídas con la entidad. 
- Pagos: Se registran los datos de las amortizaciones des las cuotas y la fecha de amortización. 


### **Beneficios Esperados:**
- Mejorar la calidad de préstamos otorgados a clientes antiguos y nuevos. 
- Asegurar el cumplimiento de pagos de los clientes.
- Identificar sectores de alto riesgo relacionados al riesgo de clientes.

## **2. Análisis: Diseño Conceptual de la base de datos.**

En esta etapa se procedió con la creación de las entidades a considerar con sus distintas relaciones entre ellas así como la llave principal para la identificación única de cada registro.

![Diagrama E_R prestamos financieros](https://github.com/Bremel1011/Bremel1011/blob/main/Diagrama%20E_R%20-%20ScoreEmpresa.jpg?raw=true)

## **3. Diseño: Definición del Modelo Lógico de la Base de Datos.**

En esta etapa se procedió con la definición de la estructura de la base de datos y su funcionalidad de tal manera que sea eficiente y evitemos duplicidad de datos o tablas.

![Modelo _logico](https://github.com/Bremel1011/Bremel1011/blob/main/score-crediticio-empresas_1.png?raw=true)

## **4. Construcción: Desarrollo de las sentencias SQL que permiten la construcción de las tablas y demás estructuras de la Base de Datos en SQL SERVER.**

Se procede con la creación de tablas y definición del tipo de variables a usar.

```
---------------------------------
--- SCORE CREDITICIO EMPRESAS ---
---------------------------------

-------TEAM
----------  PATRICIA LAHURA
----------  BREMEL ARONES

/**************************************************************
***************************************************************
**********************CREACIÓN DE TABLAS***********************
***************************************************************
***************************************************************/

--CREATE DATABASE BDG10;
USE BDG10;
GO

------- INICIO
/* DROPEAR EN ESE ORDEN PARA QUE NO HAYA ERROR CON LAS FK.

DROP TABLE SCORE;
DROP TABLE PAGOS;
DROP TABLE CUOTAS;
DROP TABLE GARANTIAS;
DROP TABLE SECTOR;
DROP TABLE ESTADOS_FINANCIEROS;
DROP TABLE PRESTAMOS;
DROP TABLE EJECUTIVO_RELACION;
DROP TABLE HISTORIAL_CREDITICIO_EXTERNO;
DROP TABLE EMPRESAS;

*/

-- 1. CREACION DE LAS TABLAS A UTILIZAR



-- DROP TABLE EMPRESAS
CREATE TABLE EMPRESAS (
    ID_EMPRESA INT PRIMARY KEY IDENTITY(1,1) ,
    NOMBRE_EMPRESA VARCHAR(255),
    FECHA_REGISTRO DATE,
    ID_SIC VARCHAR(20),
    RUC VARCHAR(20)
);

-- DROP TABLE SECTOR
CREATE TABLE SECTOR (
    ID_SECTOR INT PRIMARY KEY IDENTITY(1,1),
	ID_SIC VARCHAR(20),
    NOMBRE_SECTOR VARCHAR(255)
);

-- DROP TABLE EJECUTIVO_RELACION
CREATE TABLE EJECUTIVO_RELACION (
    ID_EJECUTIVO INT PRIMARY KEY IDENTITY(1,1) ,
    NOMBRES VARCHAR(255),
    APELLIDO_PATERNO VARCHAR(255),
    APELLIDO_MATERNO VARCHAR(255)
);

-- DROP TABLE PRESTAMOS
CREATE TABLE PRESTAMOS (
    ID_OPERACION INT PRIMARY KEY IDENTITY(1,1) ,
    ID_EMPRESA INT FOREIGN KEY REFERENCES EMPRESAS(ID_EMPRESA),
    MONTO FLOAT,
    ESTADO_PRESTAMO VARCHAR(255),
    TASA_INTERES DECIMAL,
    FECHA_INICIO DATE,
    FECHA_VENCIMIENTO DATE,
    ID_EJECUTIVO INT FOREIGN KEY REFERENCES EJECUTIVO_RELACION(ID_EJECUTIVO),
    SITUACION_CONTABLE VARCHAR(50)
);

/*ALTER TABLE PRESTAMOS
ALTER COLUMN SITUACION_CONTABLE VARCHAR(50);*/


-- DROP TABLE GARANTIAS
CREATE TABLE GARANTIAS (
    ID_GARANTIA INT PRIMARY KEY IDENTITY(1,1) ,
    TIPO_GARANTIA VARCHAR(255),
    MONTO FLOAT,
    ID_OPERACION INT FOREIGN KEY REFERENCES PRESTAMOS(ID_OPERACION)
);


--DROP TABLE CUOTAS
CREATE TABLE CUOTAS (
    id INT PRIMARY KEY IDENTITY(1,1) ,
    ID_PRESTAMOS INT FOREIGN KEY REFERENCES PRESTAMOS(ID_OPERACION),
    NUMERO_CUOTA INT NULL,
    MONTO_CUOTA DECIMAL (10,2),
    FECHA_VENCIMIENTO DATE
);


--DROP TABLE PAGOS
CREATE TABLE PAGOS (
    ID_PAGOS INT PRIMARY KEY IDENTITY(1,1) ,
    MONTO_PAGO FLOAT,
    FECHA_PAGO DATE,
    METODO_PAGO VARCHAR(255),
    DIAS_MORA INT,
    ID_CUOTAS INT FOREIGN KEY REFERENCES CUOTAS(id)
);


--DROP TABLE ESTADOS_FINANCIEROS
CREATE TABLE ESTADOS_FINANCIEROS (
    ID_EEFF INT PRIMARY KEY IDENTITY(1,1) ,
    ID_EMPRESA INT FOREIGN KEY REFERENCES EMPRESAS(ID_EMPRESA),
    ANIO DATE,
    VENTAS INT,
   -- UTILIDAD_OPE DECIMAL (10,2),
    COSTOS INT,
    DEUDA INT,
    UTILIDAD_NETA INT
);

--DROP TABLE HISTORIAL_CREDITICIO_EXTERNO
CREATE TABLE HISTORIAL_CREDITICIO_EXTERNO (
    ID_HISTORIAL INT PRIMARY KEY IDENTITY(1,1) ,
	ID_EMPRESA INT FOREIGN KEY REFERENCES EMPRESAS(ID_EMPRESA),
    RUC VARCHAR(20),
    FECHA_MES DATE,
    MORA_SF INT,
    CLASIFICACION_SBS INT,
    NUMERO_DE_IF INT,
    DEUDA_SF DECIMAL (10,2)
);

--DROP TABLE SCORE
CREATE TABLE SCORE (
    ID_SCORE INT PRIMARY KEY IDENTITY(1,1) ,
    ID_EMPRESA INT FOREIGN KEY REFERENCES EMPRESAS(ID_EMPRESA),
    FECHA_EVAL DATE,
    SCORE VARCHAR (15)
);
```

## **5. Producto: Diseño Físico, Script de la Base de datos con inserción de datos**

### Diseño Físico: En esta sección se inserta el diagrama de la base de datos generado por SQL Server.

![DiagramaSQL](https://github.com/Bremel1011/Bremel1011/blob/main/DIAGRAMA%20SQL.png?raw=true)

### Script de la base de datos: En esta sección se inserta el script de la base de datos e inserción de datos generado por SQL Server.

- Script generado por SQL: [ScriptBD](SCRIPTBDG10.sql)
  
- Script generado por SQL: [ScriptBD](SCRIPTBDG10.sql)


## **6. Anexos: Evidencias de Consultas, funciones, procedimientos almacenados, vistas, etc. realizados sobre la base de datos implementada.**

Se realizaron 10 ejercicios propuestos, en los cuales se incluye el cálculo del Score Crediticio.

```
/*************************************************************
**************************************************************
**********************CONSULTAS*******************************
**************************************************************
**************************************************************/

---- 1. Obtener todas las empresas y su sector correspondiente

SELECT E.NOMBRE_EMPRESA, S.NOMBRE_SECTOR
FROM EMPRESAS E
INNER JOIN SECTOR S ON E.ID_SIC = S.ID_SIC
WHERE NOMBRE_EMPRESA LIKE'%P%';

---- 2. Listar los préstamos activos (con estado 'ACTIVO') y su ejecutivo a cargo

SELECT P.ID_OPERACION, P.MONTO, P.TASA_INTERES, P.FECHA_INICIO, P.FECHA_VENCIMIENTO, E.NOMBRES, E.APELLIDO_PATERNO
FROM PRESTAMOS P
INNER JOIN EJECUTIVO_RELACION E ON P.ID_EJECUTIVO = E.ID_EJECUTIVO
WHERE P.ESTADO_PRESTAMO = 'ACTIVO';


---- 3. Contar el número de empresas registradas por año

SELECT YEAR(FECHA_REGISTRO) AS ANIO, COUNT(*) AS NUM_EMPRESAS
FROM EMPRESAS
GROUP BY YEAR(FECHA_REGISTRO);

---- 4. Obtener el total de deuda acumulada por empresa en los estados financieros

SELECT E.NOMBRE_EMPRESA, SUM(EF.DEUDA) AS TOTAL_DEUDA
FROM ESTADOS_FINANCIEROS EF
INNER JOIN EMPRESAS E ON EF.ID_EMPRESA = E.ID_EMPRESA
GROUP BY E.NOMBRE_EMPRESA;

---- 5. Encontrar las empresas con más de un préstamo activo

SELECT E.NOMBRE_EMPRESA, COUNT(P.ID_OPERACION) AS NUMERO_PRESTAMOS
FROM EMPRESAS E
INNER JOIN PRESTAMOS P ON E.ID_EMPRESA = P.ID_EMPRESA
WHERE P.ESTADO_PRESTAMO = 'ACTIVO'
GROUP BY E.NOMBRE_EMPRESA
HAVING COUNT(P.ID_OPERACION) <= 2;

---- 6. Obtener las garantías asociadas a un préstamo específico

SELECT G.TIPO_GARANTIA, sum(G.MONTO) AS MONTO_GARANTIZADO, 
sum(P.MONTO) AS PRESTAMO, 
ROUND(SUM(G.MONTO)/SUM(P.MONTO),2,1) AS COBERTURA
FROM GARANTIAS G
LEFT JOIN PRESTAMOS P ON G.ID_OPERACION = P.ID_OPERACION
GROUP BY G.TIPO_GARANTIA

---- 7. Calcular el score crediticio de las empresas 


UPDATE SCORE 
SET SCORE = (
    SELECT TOP 1
        CASE 
            WHEN H.MORA_SF <= 15 OR (H.CLASIFICACION_SBS = 0 AND H.NUMERO_DE_IF < 2 
			AND (H.deuda_sf / E.VENTAS) <= 0.5) THEN 'NIVEL BAJO'
            WHEN H.MORA_SF <= 50 AND H.MORA_SF > 15 AND H.CLASIFICACION_SBS = 1 
			AND (H.deuda_sf / E.VENTAS) <= 1 AND (H.deuda_sf / E.VENTAS) > 0.5 THEN 'NIVEL MEDIO'
            WHEN H.MORA_SF <= 120 AND H.MORA_SF > 50 AND H.CLASIFICACION_SBS = 2 
			AND (H.deuda_sf / E.VENTAS) <= 1.5 AND (H.deuda_sf / E.VENTAS) > 1 THEN 'NIVEL ALTO MEDIO'
            WHEN H.MORA_SF > 120 AND H.CLASIFICACION_SBS IN (3, 4) 
			AND (H.deuda_sf / E.VENTAS) > 1.5 THEN 'NIVEL ALTO'
            ELSE 'NIVEL ALTO' 
        END 
    FROM HISTORIAL_CREDITICIO_EXTERNO H
    LEFT JOIN ESTADOS_FINANCIEROS E ON H.ID_EMPRESA = E.ID_EMPRESA
    WHERE S.ID_EMPRESA = H.ID_EMPRESA
)
FROM SCORE S;


---- 8. Conocer el score para cada empresa con prestamos vigentes y sector asociado

-- CREAMOS TABLA TEMPORAL 
SELECT  A.ID_EMPRESA, A.RUC, A.NOMBRE_EMPRESA, B.NOMBRE_SECTOR INTO #TEMP1
FROM
EMPRESAS A LEFT JOIN SECTOR B ON A.ID_SIC = B.ID_SIC;

-- CREAMOS CONSULTA
SELECT A.ID_EMPRESA,A.NOMBRE_EMPRESA, C.SITUACION_CONTABLE, A.NOMBRE_SECTOR,B.SCORE
FROM #TEMP1 A
LEFT JOIN SCORE B ON A.ID_EMPRESA = B.ID_EMPRESA 
LEFT JOIN PRESTAMOS C ON A.ID_EMPRESA = C.ID_EMPRESA 
WHERE C.SITUACION_CONTABLE IN ('VIGENTE');

--DROP TABLE #TEMP1



---- 9. Procedure para registrar un nuevo préstamo: Este procedimiento permite registrar un nuevo 
----préstamo asociado a una empresa, un ejecutivo, y genera una garantía inicial si es proporcionada.

CREATE PROCEDURE RegistrarPrestamo
    @ID_EMPRESA INT,
    @MONTO FLOAT,
    @ESTADO_PRESTAMO VARCHAR(255),
    @TASA_INTERES DECIMAL(10,2),
    @FECHA_INICIO DATE,
    @FECHA_VENCIMIENTO DATE,
    @ID_EJECUTIVO INT,
    @SITUACION_CONTABLE VARCHAR(50),
    @TIPO_GARANTIA VARCHAR(255) = NULL,
    @MONTO_GARANTIA FLOAT = NULL
AS
BEGIN
    -- Insertar nuevo préstamo
    INSERT INTO PRESTAMOS (ID_EMPRESA, MONTO, ESTADO_PRESTAMO, TASA_INTERES, FECHA_INICIO, FECHA_VENCIMIENTO, ID_EJECUTIVO, SITUACION_CONTABLE)
    VALUES (@ID_EMPRESA, @MONTO, @ESTADO_PRESTAMO, @TASA_INTERES, @FECHA_INICIO, @FECHA_VENCIMIENTO, @ID_EJECUTIVO, @SITUACION_CONTABLE);

	-- Obtener el ID del préstamo recién insertado
    DECLARE @ID_OPERACION INT;
    SET @ID_OPERACION = SCOPE_IDENTITY(); 

    -- Insertar garantía asociada, si se proporcionó
    IF @TIPO_GARANTIA IS NOT NULL AND @MONTO_GARANTIA IS NOT NULL
    BEGIN
        INSERT INTO GARANTIAS (TIPO_GARANTIA, MONTO, ID_OPERACION)
        VALUES (@TIPO_GARANTIA, @MONTO_GARANTIA, @ID_OPERACION);
    END;
END;


---- 10. Procedure para consultar la situación financiera de una empresa
---- Este procedimiento muestra el estado financiero (ventas, costos, deuda, utilidad neta) de una empresa en un año específico.

CREATE PROCEDURE ConsultarSituacionFinancieraEmpresa
    @ID_EMPRESA INT,
    @ANIO DATE
AS
BEGIN
    SELECT VENTAS, COSTOS, DEUDA, UTILIDAD_NETA
    FROM ESTADOS_FINANCIEROS
    WHERE ID_EMPRESA = @ID_EMPRESA AND ANIO = @ANIO;
END;
```

> [!IMPORTANT]
> Se adjunta el Script final que incluye la creación de tablas, inserción de datos y consultas:
> [ScriptFinal](SCORE_FINANCIERO_VF.sql)


