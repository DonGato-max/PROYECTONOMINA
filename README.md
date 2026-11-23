# Nómina System

Este proyecto lo estoy desarrollando como una aplicación de escritorio para llevar el control de la información del personal y apoyar el proceso de nómina quincenal.

La idea es tener en un mismo sistema los datos de la empresa, cargos, empleados, contratos, ingresos, deducciones, subsidios y los registros relacionados con planillas, vacaciones, décimo tercer mes y liquidaciones.

## Tecnologías utilizadas

- C# y Windows Forms
- .NET 8 para Windows
- Visual Studio 2022
- SQL Server 2022
- Microsoft.Data.SqlClient

## Archivos del proyecto

- [Documento de análisis, diseño e implementación](Documentation/Analisis_Diseno_Implementacion.pdf)
- [Paquete de base de datos SQL Server](Database/NominaSystem_BaseDatos.zip)

## Base de datos

La base de datos se llama `NominaSystemDB`. El paquete SQL incluye el script para crear la estructura y otro archivo con datos ficticios para probar algunos catálogos y relaciones.

Para instalarla, abre SQL Server Management Studio, conéctate a tu instancia y ejecuta `NominaSystemDB.sql`. Después puedes ejecutar `DatosPrueba.sql`. Los registros de prueba no corresponden a personas ni empresas reales.

La conexión de la aplicación se configura en `Data/ConexionDB.cs`. Si utilizas otra instancia de SQL Server, debes ajustar el nombre del servidor.

## Módulos contemplados

1. Empresa
2. Cargos
3. Empleados
4. Tipo de contrato
5. Contratos
6. Deducciones
7. Otros ingresos
8. Subsidios
9. Planilla
10. Vacaciones
11. Décimo tercer mes
12. Liquidaciones

## Organización

- `Forms`: ventanas y formularios.
- `Models`: clases de datos.
- `Repositories`: consultas y operaciones con la base de datos.
- `Services`: seguridad y manejo de sesión.
- `Data`: conexión con SQL Server.
- `Helpers`: clases auxiliares de la interfaz.
- `Documentation`: documento del proyecto.
- `Database`: scripts de creación y datos de prueba.

## Abrir el proyecto

1. Abrir `nominaSystem.sln` con Visual Studio 2022.
2. Restaurar los paquetes NuGet.
3. Verificar que SQL Server esté iniciado y que la base de datos exista.
4. Revisar la cadena de conexión.
5. Ejecutar el proyecto desde Visual Studio.

## Estado del proyecto

El proyecto se encuentra en desarrollo. La publicación del código no significa que todos los módulos estén terminados ni que los cálculos legales estén certificados. Antes de utilizarlo con información real, se deben completar las pruebas en Visual Studio y SQL Server y verificar los parámetros de nómina conforme a la normativa vigente.
