# Evaluación de Seguridad — FinTrack Solutions

> Repositorio de portafolio. Evaluación de seguridad para FinTrack Solutions,
> una fintech ficticia. Documenta un proyecto de consultoría de principio a fin.

## Sobre este proyecto

FinTrack Solutions es una fintech ficticia de gestión de finanzas personales: 28.000 clientes vinculan sus cuentas bancarias, categorizan gastos y programan transferencias, sostenidos por 45 empleados y un equipo de TI de 4 personas sin especialista en seguridad. Tres meses antes de este encargo, un phishing le costó seis días de acceso indebido a su correo corporativo — y la certeza de que no podía saber qué había pasado. Este repositorio documenta la evaluación completa que responde a ese incidente: del modelo de amenazas a la hoja de ruta, con cada control trazado al riesgo que lo exige y con los descartes argumentados por escrito.

## Rol asumido

Consultor de seguridad externo, contratado por la dirección tras el incidente. Sin equipo, sin presupuesto extra: cada recomendación debe poder operarla el equipo de TI actual.

## Stack de FinTrack (ficticio, representativo)

- Aplicación web y móvil sobre una API en nube pública (un solo proveedor).
- Base de datos gestionada con los datos financieros de los clientes.
- Agregador bancario y banco socio como terceros críticos (vinculación de cuentas y rieles de transferencia).
- Suite de correo/colaboración corporativa y GitHub para código y despliegue.

## Estructura

- `01-business-case.md` — el cliente, sus activos y el alcance
- `02-architecture/` — arquitectura y superficie de ataque
- `03-risk-analysis/` — modelo de amenazas y registro de riesgos R1–R6
- `04-security-controls/` — controles propuestos C1–C6
- `05-policies/` — políticas mínimas viables
- `06-incident-response/` — plan de respuesta y playbook
- `07-postmortem/` — postmortem del incidente
- `08-recommendations.md` — recomendaciones finales

## Cómo navegar el repo

En orden: cada documento abre con la **decisión que permite tomar** y cierra apuntando al siguiente. La trazabilidad es la columna vertebral: los activos (A1–A5) del caso de negocio generan los riesgos (R1–R6) del registro, cada riesgo tiene exactamente un control (C1–C6), y cada política cita el R# que la exige. Los diagramas están en Mermaid dentro de los `.md` (convenciones en `02-architecture/diagram-conventions.md`). Con prisa: `08-recommendations.md` resume todo en dos páginas.

## Tesis

**El perímetro de FinTrack no es la red: es la identidad.** Todos sus caminos de ataque realistas empiezan con una credencial válida en las manos equivocadas — la de un empleado (R1), la de un cliente (R2), la heredada por un tercero (R4) o la que nadie apagó (R6) — y por eso los controles se concentran en autenticación, visibilidad y ciclo de vida de accesos antes que en cajas de red.

## Autor

Fabricio Díaz · [LinkedIn] · [GitHub]
