---
name: ferney
display_name: Ferney Acosta Quimbaya
role: Programador .NET C#
contacto: facosta@orf.com.co
---

# Ferney Acosta Quimbaya

Programador .NET C# enfocado en desarrollo y mantenimiento de aplicaciones empresariales (escritorio y web) sobre arquitecturas orientadas al dominio, servicios WCF y SQL Server. Trabaja en procesos administrativos, operativos y de talento humano.

## Stack y tecnologías

- **Escritorio:** WinForms con DevExpress 14.7, arquitectura orientada al dominio, IoC, interfaces y clases desacopladas, consumo de servicios WCF.
- **Web:** ASP.NET WebForms (.aspx) con JavaScript y Bootstrap (versiones clásicas), también consumiendo servicios WCF.
- **Datos:** SQL Server — procedimientos almacenados, vistas, funciones y consultas directas.

## Dominios que maneja

| Dominio | Estado | Suplente principal |
|---|---|---|
| Nómina (módulo principal) | Activo | _(pendiente)_ |
| Gastos de viaje | Activo | _(pendiente)_ |
| Vacaciones (solicitudes y aprobaciones) | Activo | _(pendiente)_ |
| Báscula y ajuste de tiquetes | Activo | _(pendiente)_ |
| Flujos de aprobación, consultas y reportes operativos | Activo | _(pendiente)_ |

### Detalle del módulo de Nómina

Es el módulo más relevante que mantiene. Cubre:

- Liquidación de nómina.
- Liquidación de contratos.
- Liquidación de vacaciones.
- Gestión de novedades de ausencias e incapacidades.
- Novedades que afectan el pago de nómina.
- Registro de novedades de pago (conceptos de pago y descuento).
- Gestión de bonos y conceptos asociados al cálculo de nómina.

**Regla de negocio clave — conceptos de nómina:**

- Conceptos que inician con **`P`** → representan **Pagos**.
- Conceptos que inician con **`D`** → representan **Descuentos**.

## Suplentes

Si Ferney no está disponible y alguien necesita trabajar sus dominios:

- _(pendiente definir suplentes por dominio)_

## Cómo entrar a sus dominios

1. Empezar por `personas/ferney/temas/<dominio>/README.md` (contexto).
2. Leer `glosario.md` (términos del negocio, si existe).
3. Si hay `WORKFLOW.md`, seguir esos pasos.
4. Las skills individuales se cargan según la tarea específica.

## Notas para sustitutos

- En Nómina recordar la convención de conceptos: `P*` = pagos, `D*` = descuentos.
- _(pendiente — fechas críticas de cierre de nómina, contactos de escalamiento, particularidades por empresa en el entorno multi-tenant)_
