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

### Script de la base de datos: 

[ScripBD](docs/CONTRIBUTING.md)






