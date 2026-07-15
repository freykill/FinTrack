# 01 — Caso de Negocio

**Decisión que permite tomar este documento:** qué protege FinTrack, cuánto puede sostener su equipo, y qué queda dentro y fuera del alcance de esta evaluación.

---

## El cliente

**FinTrack Solutions** es una fintech de gestión de finanzas personales. Sus usuarios vinculan cuentas bancarias, categorizan gastos y programan transferencias desde una app web y móvil.

| Dato | Valor |
|------|-------|
| Empleados | 45 |
| Fundada | 2022 |
| Clientes activos | ~28.000 |
| Infraestructura | nube pública, un solo proveedor |
| Equipo de TI | 4 personas (1 soporte, 2 desarrollo, 1 infraestructura) |
| Equipo de seguridad dedicado | ninguno |

Estas cifras no son contexto de relleno: son **la restricción de diseño de todo el informe**. Cuatro personas sin especialista en seguridad no pueden operar controles pensados para una empresa de mil. Un control que FinTrack no puede sostener no es un control — es una recomendación que se ignora en seis meses.

---

## Qué protegemos

> **FinTrack protege el dinero y los datos financieros de 28.000 personas. Si un atacante entra, la pérdida no es solo económica: es la confianza que sostiene el negocio.**

Esa frase es el criterio de decisión de este repositorio. Cuando una recomendación no se pueda justificar contra ella, sobra.

---

## Activos críticos

Un activo es crítico si su pérdida, alteración o indisponibilidad causa daño real al negocio.

| ID | Activo | Por qué es crítico | Pilar más expuesto |
|----|--------|--------------------|--------------------|
| A1 | Base de datos de clientes | su filtración destruye la confianza y expone a los usuarios a fraude | Confidencialidad |
| A2 | Motor de transferencias | alterar una transacción mueve dinero real | Integridad |
| A3 | Plataforma web y móvil | sin servicio, los clientes no operan y se van | Disponibilidad |
| A4 | Credenciales y sesiones de empleados | son la llave a todo lo anterior | Confidencialidad |
| A5 | Integraciones con terceros | acceso heredado que FinTrack concede pero no controla | según el caso |

**Observación:** A4 no es un activo de negocio, es un activo de acceso. Aparece en la lista porque comprometerlo entrega A1, A2 y A3 sin tocar ninguno directamente. Es el patrón de casi todos los incidentes recientes.

---

## El incidente que originó este encargo

Hace tres meses, un empleado del área financiera recibió un correo que suplantaba a un proveedor conocido. Ingresó sus credenciales en una página falsa. El atacante mantuvo acceso a su correo corporativo durante **seis días**.

Hechos establecidos:

- Hubo acceso a correo interno con información de clientes.
- No se disparó ninguna alerta. No existía sistema que pudiera dispararla.
- El incidente lo detectó un compañero al notar respuestas extrañas en un hilo.
- No existía un plan de respuesta escrito. La reacción se improvisó.
- No se confirmó pérdida económica, pero tampoco se pudo descartar: no había registros suficientes para saberlo.

Ese último punto es el más grave. **FinTrack no puede afirmar qué pasó durante esos seis días.** La ausencia de evidencia no es evidencia de ausencia.

---

## Las tres preguntas del encargo

La dirección contrató esta evaluación para responder:

1. **¿Qué tan expuestos estamos hoy?** → `02-architecture/` y `03-risk-analysis/`
2. **¿Qué controles necesitamos, dimensionados a nuestro tamaño?** → `04-security-controls/` y `05-policies/`
3. **¿Qué hacemos la próxima vez que pase algo?** → `06-incident-response/` y `07-postmortem/`

Todo documento de este repositorio debe poder trazarse a una de esas tres preguntas.

---

## Alcance

**Incluido:**
- Modelo de amenazas y registro de riesgos
- Arquitectura y superficie de ataque
- Controles priorizados con análisis de alternativas
- Políticas mínimas viables
- Plan de respuesta a incidentes y un playbook operativo
- Postmortem del incidente de phishing
- Hoja de ruta escalonada

**Excluido, y por qué:**

| Fuera del alcance | Razón |
|-------------------|-------|
| Pruebas de penetración | Los controles básicos no existen. Medir una puerta que sabemos abierta no aporta información. |
| Certificación ISO 27001 | Certificaría un proceso que aún no existe. Prematuro. |
| Migración de infraestructura | Es un problema de arquitectura, no de seguridad. |
| Defensa contra actores estatales | FinTrack no es un objetivo estratégico. Presupuestarlo sería malgastarlo. |

---

## Restricciones que condicionan toda recomendación

- **Presupuesto limitado.** Se priorizan controles gratuitos o de bajo costo.
- **Sin personal de seguridad.** Todo control debe ser operable por cuatro personas de TI con otras responsabilidades.
- **Datos financieros de personas.** La protección de datos personales es una obligación legal, no una elección.
- **Sin ventanas de mantenimiento amplias.** Los cambios que puedan romper producción requieren plan de reversión.

---

## Criterio de éxito

Al cierre del encargo, FinTrack debe poder:

1. Nombrar sus seis riesgos principales y qué control mitiga cada uno.
2. Saber quién hace qué en las primeras dos horas de un incidente.
3. Detectar un acceso sospechoso sin depender de que un compañero lo note.

Si al terminar no se cumplen esas tres, el encargo falló, sin importar cuántos documentos se hayan entregado.

---

## Siguiente documento

`02-architecture/architecture-overview.md` — arquitectura, superficie de ataque y opciones de control.
