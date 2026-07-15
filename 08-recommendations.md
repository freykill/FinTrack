# 08 — Recomendaciones Finales

**Decisión que permite tomar este documento:** qué hace FinTrack, en qué orden, con lo que tiene.

---

## Resumen ejecutivo

FinTrack no tiene un problema de tecnología: tiene un problema de identidad y de visibilidad. Su infraestructura de nube es estándar y razonable; lo que falta es que una contraseña robada no baste para entrar (hoy basta — pasó en abril), y que alguien se entere cuando alguien entra (hoy nadie se entera — pasaron seis días). El perímetro real de la empresa no es la red: son las credenciales de 45 empleados y 28.000 clientes, más la confianza heredada de su agregador bancario y su banco socio.

La recomendación central cabe en una frase: **cerrar primero lo que ya se demostró explotable, con capacidades que en su mayoría ya están pagadas.** MFA resistente a phishing y endurecimiento del correo (C1) cierran el vector del incidente real. Registros y seis alertas de alta señal (C3) convierten la próxima intrusión en un evento de minutos y no de días. Respaldos inmutables fuera de la cuenta principal (C5) ponen un piso al peor escenario. Esas tres cosas caben en un mes de trabajo de un equipo de 4 y su costo directo es marginal. Después vienen el proceso (accesos, terceros — C4/C6) y el trabajo de producto (login de clientes — C2), que se planifica en el trimestre.

Igual de importante es lo que **no** recomendamos hoy: SIEM comercial, SOC/MDR 24/7, WAF gestionado, pentest inmediato, certificaciones y DLP. No porque sean malos, sino porque están dimensionados para otra empresa: consumen presupuesto y atención que FinTrack necesita en los básicos, y varios de ellos vigilan una casa a la que todavía le faltan cerraduras. Cada descarte tiene su argumento en `04-security-controls/`; varios tienen fecha de reevaluación, no rechazo eterno.

## Hoja de ruta

| Cuándo | Acción | Riesgo que cierra | Esfuerzo |
|--------|--------|-------------------|----------|
| Esta semana | MFA obligatorio: llaves FIDO2 en TI/administración, TOTP en el resto (C1) | R1 | Bajo — capacidad ya pagada + ~USD 200 en llaves |
| Esta semana | SPF/DKIM/DMARC en modo rechazo (C1) | R1 | Bajo — configuración |
| Esta semana | Copia inmutable de respaldos en cuenta separada (C5) | R5 | Medio — configuración delicada, un día de trabajo |
| Esta semana | Iniciar inventario de terceros: qué datos y scopes tiene cada uno (C4) | R4 | Bajo — una tabla, medio día |
| Este mes | Registros centralizados, retención 12 meses + 6 alertas de alta señal (C3) | R3 | Medio — 2-3 días de configuración |
| Este mes | Primera restauración de prueba, documentada y cronometrada (C5) | R5 | Bajo — medio día al mes |
| Este mes | Checklist de alta/baja + primera revisión trimestral de accesos (C6) | R6 | Bajo — disciplina más que horas |
| Este mes | Rotación inicial de llaves/tokens de terceros + cláusula de notificación 72 h en contratos nuevos (C4) | R4 | Medio — coordinación con terceros |
| Este mes | Sesión de 30 min al equipo: cómo reportar sin miedo + verificación telefónica de cambios bancarios de proveedores | R1 | Bajo |
| Este trimestre | MFA por pasos en acciones sensibles + límite de intentos en el login de clientes (C2) | R2 | Alto — semanas de los 2 devs, se planifica como feature |
| Este trimestre | SSO donde el plan del SaaS lo permita + cifrado de disco verificado en todas las laptops (C6) | R6 | Medio |
| 12 meses | Simulacro completo de incidente (correo de prueba + regla de reenvío) y medición contra el playbook | R1, R3 | Bajo |
| 12 meses | Reevaluar MDR con los básicos ya operando; pentest ahora sí, contra controles reales | R1–R6 (verificación) | Medio — primer gasto externo significativo |
| 12 meses | Reevaluar WAF/gestión de bots con datos reales de C3; SOC 2 si un cliente enterprise lo exige | R2 | Según datos |

## Lo que deliberadamente no recomendamos

Recomendar todo es no recomendar nada. Los descartes, consolidados — el argumento completo de cada uno está en `04-security-controls/security-controls.md`:

| Descartado | Por qué |
|-----------|---------|
| SIEM comercial | Sin analista dedicado, es gasto sin detección. Los logs nativos + 6 alertas dan el 80% del valor. |
| SOC propio / MDR hoy | Vigilancia experta sobre una casa sin cerraduras. Reevaluación con fecha: 12 meses. |
| Pentest inmediato | Confirmaría lo que ya sabemos. Tiene fecha, no rechazo: cuando existan controles que probar. |
| ISO 27001 / SOC 2 hoy | Certificar procesos que no existen. Se activa cuando sea requisito comercial real. |
| WAF con gestión de bots hoy | El rate limiting de C2 resuelve la mayor parte; el WAF entra si los datos de C3 lo justifican. |
| DLP enterprise | Exige clasificación madura y triaje que 4 personas no pueden sostener. |
| Zero Trust integral / microsegmentación | Arquitectura destino, no proyecto trimestral. Sus piezas útiles ya están en C1/C4/C6. |
| Simulacros de phishing punitivos | Destruyen el reporte temprano, que fue lo único que funcionó en abril. |
| Defensa contra APT/estatales | Fuera del modelo de amenazas. El presupuesto se queda donde están los atacantes reales. |

## Cierre

El criterio de éxito del encargo (`01-business-case.md`) era triple: que FinTrack pueda nombrar sus seis riesgos y el control de cada uno (este repositorio: R1–R6 → C1–C6), que sepa quién hace qué en las primeras dos horas de un incidente (`06-incident-response/`), y que detecte un acceso sospechoso sin depender de la suerte (C3, este mes). Lo que queda no es análisis: es ejecución — y está dimensionada para que cuatro personas puedan, de verdad, ejecutarla.
