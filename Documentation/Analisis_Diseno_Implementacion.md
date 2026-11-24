# Nómina System
## Documento de análisis, diseño e implementación

**Proyecto:** Sistema de gestión y emisión de nómina quincenal  
**Plataforma:** Windows Forms · C# · .NET 8 · SQL Server 2022  
**Versión documental:** 1.0 · 22 de noviembre de 2026

> Este documento describe la estructura observada en los archivos del proyecto. Distingue entre los componentes identificados en el código, los objetos definidos en el script SQL y los aspectos que todavía requieren pruebas. No constituye una certificación de cumplimiento legal de los cálculos de nómina.

## Control del documento

| Elemento | Descripción |
|---|---|
| Nombre del sistema | Nómina System |
| Tipo de aplicación | Aplicación de escritorio Windows Forms |
| Lenguaje y plataforma | C# sobre .NET 8 para Windows |
| Acceso a datos | ADO.NET mediante Microsoft.Data.SqlClient |
| Motor de base de datos | Microsoft SQL Server 2022 |
| Base de datos | NominaSystemDB |

**Estado técnico:** el entorno de revisión no dispone del SDK de .NET, por lo que no se pudo ejecutar una compilación de la solución. Este documento no afirma que el ejecutable haya sido compilado ni que todos los flujos estén probados.

## 1. Introducción

Nómina System es una aplicación de escritorio orientada a centralizar información laboral y apoyar el proceso de preparación de nóminas quincenales. El sistema contempla la administración de empresas, cargos, empleados, contratos, conceptos de ingresos y deducciones, subsidios, períodos de nómina, planillas, vacaciones, décimo tercer mes y liquidaciones.

La solución utiliza una interfaz Windows Forms en C#, una capa de acceso a datos basada en ADO.NET y una base de datos relacional en SQL Server. El diseño de la base de datos incorpora claves primarias, claves foráneas, restricciones, índices, vistas y procedimientos almacenados.

## 2. Análisis del problema

En una gestión manual o dispersa de nómina, los datos de empleados, contratos, conceptos salariales y períodos pueden mantenerse en registros separados. Esto dificulta consultar información consistente, mantener el historial y preparar los datos de cada período. El sistema propuesto concentra estos datos en una base relacional y proporciona módulos para su administración.

### 2.1 Objetivo general

Desarrollar una aplicación de escritorio que permita administrar la información laboral y organizar el proceso de nómina quincenal mediante módulos conectados a una base de datos SQL Server.

### 2.2 Objetivos específicos

- Administrar empresas, cargos, empleados, tipos de contrato y contratos.
- Mantener catálogos de ingresos, deducciones y subsidios.
- Registrar períodos y planillas de nómina.
- Gestionar información relacionada con vacaciones, décimo tercer mes y liquidaciones.
- Controlar el acceso mediante usuarios y roles.
- Mantener trazabilidad de operaciones mediante registros de auditoría.

### 2.3 Alcance funcional

| Módulo | Responsabilidad prevista |
|---|---|
| Empresa | Datos generales de la empresa. |
| Cargos | Catálogo de cargos asociados a una empresa. |
| Empleados | Datos personales, laborales y estado del empleado. |
| Tipo de contrato | Clasificación de los contratos laborales. |
| Contratos | Relación laboral, fechas y condiciones contractuales. |
| Deducciones | Movimientos y conceptos de deducción. |
| Otros ingresos | Movimientos y conceptos de ingreso. |
| Subsidios | Registro de subsidios asociados a empleados. |
| Planilla | Períodos, borradores, movimientos y totales de nómina. |
| Vacaciones | Solicitudes y movimientos de vacaciones. |
| Treceavo mes | Acumulación y detalle del décimo tercer mes. |
| Liquidaciones | Registro de liquidaciones y sus conceptos. |

## 3. Requisitos del sistema

### 3.1 Requisitos funcionales

- **RF-01.** El sistema debe presentar una pantalla de inicio de sesión.
- **RF-02.** El acceso debe validar las credenciales contra los usuarios almacenados.
- **RF-03.** El sistema debe considerar roles de usuario para organizar los permisos.
- **RF-04.** Debe permitir consultar y mantener registros de los módulos incluidos en el alcance.
- **RF-05.** Debe identificar relaciones entre tablas mediante claves foráneas y ofrecer opciones relacionadas cuando corresponda.
- **RF-06.** Debe permitir buscar registros en los módulos.
- **RF-07.** Debe permitir guardar cambios y eliminar registros seleccionados, sujeto a las restricciones de integridad.
- **RF-08.** En el módulo Planilla debe permitir crear un borrador, cargar movimientos y recalcular totales mediante procedimientos almacenados.

### 3.2 Requisitos no funcionales

- Plataforma de escritorio para Windows, desarrollada con .NET 8 y Windows Forms.
- Persistencia centralizada en SQL Server 2022.
- Comunicación con SQL Server mediante Microsoft.Data.SqlClient.
- Integridad referencial mediante claves primarias y foráneas.
- Contraseñas almacenadas como hash y no como texto plano.
- Parámetros y tasas legales configurables y sujetos a verificación antes de utilizarse en nóminas reales.

## 4. Diseño de la solución

### 4.1 Arquitectura lógica

- **Interfaz (Forms):** FrmLogin, FrmPrincipal y FrmModulo.
- **Modelos (Models):** objetos que representan datos del usuario y la sesión.
- **Repositorios (Repositories):** UsuarioRepository y ConsultaRepository para ejecutar consultas y operaciones de persistencia.
- **Servicios (Services):** SeguridadService para hash/verificación de contraseñas y SesionActual para mantener el usuario autenticado.
- **Datos (Data):** ConexionDB centraliza la creación de conexiones SQL Server.
- **Helpers:** EstilosSistema contiene elementos reutilizables de presentación.

### 4.2 Flujo general

1. La aplicación inicia en FrmLogin.
2. UsuarioRepository valida el usuario y la contraseña.
3. Si las credenciales son aceptadas, se establece la sesión y se abre FrmPrincipal.
4. FrmPrincipal presenta los accesos a los módulos.
5. FrmModulo consulta los datos del módulo seleccionado mediante ConsultaRepository.
6. ConsultaRepository utiliza ConexionDB para conectarse a NominaSystemDB.
7. Los cambios se envían a SQL Server y se respetan las relaciones y restricciones definidas en la base de datos.

### 4.3 Diseño de interfaz y seguridad

La interfaz se organiza en una pantalla de acceso, una ventana principal con menú de módulos y un control reutilizable para operaciones de consulta y mantenimiento. FrmModulo incorpora búsqueda, tabla de datos, botones de agregar, guardar, eliminar y actualizar. Para el módulo Planilla incorpora acciones de generación de borrador, carga de movimientos y recálculo.

La base de datos contempla las entidades Usuario, Rol y UsuarioRol. El código incluye un servicio de seguridad para generar y verificar hashes de contraseña. La cadena de conexión observada utiliza autenticación integrada de Windows y apunta a la instancia local identificada como DONGATO. Antes de distribuir el sistema, la cadena debe configurarse por ambiente y no debe publicarse información sensible.

## 5. Diseño de la base de datos

La base de datos NominaSystemDB está diseñada para SQL Server 2022. El script entregado contiene **31 definiciones de tablas, 3 vistas y 5 procedimientos almacenados**. Las tablas se organizan por áreas funcionales; se emplean claves primarias, claves foráneas, restricciones CHECK, restricciones UNIQUE e índices para apoyar la integridad y la consulta.

### 5.1 Inventario de tablas

| Área | Tablas |
|---|---|
| Organización y catálogos | Empresa, PeriodicidadNomina, TipoContrato, Cargo, TipoIngreso, TipoDeduccion, TipoSubsidio |
| Seguridad | Rol, Usuario, UsuarioRol, Auditoria |
| Personal y contratación | Empleado, Contrato |
| Períodos y planillas | PeriodoNomina, Planilla, PlanillaDetalle |
| Movimientos de nómina | MovimientoIngreso, MovimientoDeduccion, PlanillaDetalleIngreso, PlanillaDetalleDeduccion, PlanillaDetalleAportePatronal, Subsidio |
| Prestaciones y salidas | SolicitudVacacion, VacacionMovimiento, DecimoTercerMes, DecimoTercerMesDetalle, Liquidacion, LiquidacionDetalle |
| Parámetros tributarios | TablaIR, TablaIRTramo, ParametroNomina |

### 5.2 Vistas

- **vw_EmpleadosActivos:** consulta de empleados activos.
- **vw_PlanillaResumen:** resumen de información de planillas.
- **vw_SaldoVacaciones:** consulta de saldos de vacaciones.

### 5.3 Procedimientos almacenados

- **usp_PeriodoNomina_Crear:** creación de períodos de nómina.
- **usp_Planilla_CrearBorrador:** creación de una planilla en estado borrador y copia de contratos vigentes.
- **usp_Planilla_CargarMovimientos:** incorporación de movimientos asociados a la planilla.
- **usp_Planilla_RecalcularTotales:** recálculo de totales de planilla.
- **usp_Auditoria_Registrar:** registro de eventos de auditoría.

### 5.4 Relaciones principales

Empresa se relaciona con cargos, empleados y usuarios. Empleado se relaciona con contratos y movimientos laborales. Contrato depende del empleado y del tipo de contrato. PeriodoNomina organiza la ejecución por período y se relaciona con Planilla. Planilla se desglosa en detalles y conceptos de ingresos, deducciones y aportes patronales. Las tablas de vacaciones, décimo tercer mes y liquidaciones conservan sus propios registros y detalles. UsuarioRol vincula usuarios con roles.

### 5.5 Reglas y precauciones

El script no fija tasas ni fórmulas legales de INSS, IR, vacaciones o décimo tercer mes. Los parámetros deben configurarse y verificarse según la normativa vigente antes de procesar una nómina real. El procedimiento de generación crea un borrador y copia contratos vigentes; no debe interpretarse como cálculo automático definitivo de todos los importes legales.

## 6. Implementación

### 6.1 Tecnologías

- Visual Studio 2022 como entorno de desarrollo.
- C# y Windows Forms sobre .NET 8 para Windows.
- Microsoft.Data.SqlClient 6.1.3 como proveedor SQL Server declarado en el proyecto.
- SQL Server 2022 como motor de base de datos.
- ADO.NET para consultas, lectura y actualización de datos.

### 6.2 Componentes identificados en el código

- **Program.cs:** punto de entrada; inicia la aplicación en FrmLogin.
- **FrmLogin:** interfaz de autenticación.
- **FrmPrincipal:** ventana principal y navegación entre módulos.
- **FrmModulo:** control reutilizable para listar, buscar, agregar, guardar y eliminar registros; aplica controles de selección para relaciones detectadas.
- **UsuarioRepository:** autenticación del usuario.
- **ConsultaRepository:** consultas, relaciones, opciones de claves foráneas, guardado y ejecución de procedimientos.
- **SeguridadService:** generación y verificación de hashes de contraseña.
- **SesionActual:** mantiene la información del usuario autenticado.
- **ConexionDB:** centraliza la conexión a SQL Server.
- **EstilosSistema:** contiene elementos reutilizables de presentación.

### 6.3 Configuración de conexión

La aplicación utiliza Microsoft.Data.SqlClient para conectarse a SQL Server. En el entorno de desarrollo se identifica la instancia DONGATO y la base NominaSystemDB, con autenticación integrada de Windows. Si se instala en otro equipo, se debe revisar el nombre de la instancia y los permisos de acceso.

## 7. Instalación de la base de datos

1. Instalar o disponer de SQL Server 2022.
2. Abrir SQL Server Management Studio.
3. Conectarse a la instancia de SQL Server correspondiente.
4. Ejecutar el archivo NominaSystemDB.sql para crear la base y sus objetos.
5. Si se desea probar con información ficticia, ejecutar DatosPrueba.sql.
6. Revisar la cadena de conexión de la aplicación antes de iniciar el sistema.

Los registros incluidos para pruebas son ficticios y no corresponden a personas ni empresas reales.

## 8. Estado actual, limitaciones y trabajo pendiente

El proyecto se encuentra en desarrollo. La documentación y los scripts describen la estructura disponible, pero no significan que todos los módulos estén terminados o que los cálculos legales estén certificados.

Antes de utilizarlo con información real se requiere:

- Compilar y ejecutar la solución en Visual Studio 2022 con .NET 8.
- Probar la conexión con SQL Server en el equipo donde se instalará.
- Ejecutar y validar los scripts de creación y datos de prueba.
- Probar los flujos de autenticación, mantenimiento de catálogos y operaciones de planilla.
- Verificar las fórmulas, tasas y parámetros de nómina conforme a la normativa vigente.
- Realizar pruebas con distintos períodos, empleados y situaciones laborales.

## 9. Conclusión

Nómina System plantea una solución de escritorio para organizar la información del personal y apoyar la preparación de nóminas quincenales. Su diseño separa la interfaz, el acceso a datos y la persistencia en SQL Server. La base de datos contempla las áreas principales del proceso y mantiene relaciones entre los registros.

El sistema debe continuar con pruebas funcionales y validación de los parámetros legales antes de considerarse listo para operar con información real.

---
**Nómina System · Documento técnico del proyecto académico**