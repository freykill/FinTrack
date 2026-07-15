# 07 — Postmortem: Incidente de phishing (abril 2026)

**Cultura:** este postmortem es sin culpables (blameless). Se busca la causa,
no la persona. Con 45 personas recibiendo correo a diario, alguien siempre iba
a hacer clic; la pregunta del documento es por qué un clic bastó.

---

## Resumen

Un empleado del área financiera recibió un correo que suplantaba a un proveedor habitual e ingresó sus credenciales en una página falsa. El atacante mantuvo acceso al correo corporativo durante **seis días**, sin generar ninguna alerta, hasta que un compañero notó respuestas extrañas en un hilo. La contención inicial fue incompleta (se cambió la contraseña sin revocar sesiones) y la investigación posterior no pudo determinar el alcance real del acceso por falta de registros. No se confirmó pérdida económica — pero tampoco se pudo descartar.

## Línea de tiempo

Los eventos marcados *(reconstruido)* se armaron después, con los pocos registros disponibles; su hora es aproximada. Que haya que marcarlos así **es en sí un hallazgo**.

| Día / hora | Evento |
|------------|--------|
| Día 0, 10:14 | Llega el correo "factura actualizada" suplantando al proveedor habitual. Pasa los filtros: el dominio remitente varía en una letra. |
| Día 0, 10:32 | El empleado ingresa usuario y contraseña en la página falsa. |
| Día 0, 11:05 | *(reconstruido)* Primer inicio de sesión del atacante, desde IP extranjera. Ninguna alerta — no existía ninguna. |
| Día 0, 11:20 | *(reconstruido)* El atacante crea una regla de correo que le reenvía mensajes con términos de pago y los archiva localmente. |
| Días 1–5 | *(no determinable)* Acceso continuado. Qué se leyó o exportó no pudo establecerse: no había registro de actividad del buzón. |
| Día 5 | *(reconstruido)* El atacante responde dentro de un hilo real con un proveedor, intentando redirigir un pago a otra cuenta bancaria. |
| Día 6, 09:40 | Un compañero nota respuestas extrañas en el hilo y avisa por chat. Detección: humana y por suerte. |
| Día 6, 10:15 | Se cambia la contraseña de la cuenta. **No** se cierran las sesiones activas ni se revocan tokens: el atacante conserva acceso un tiempo más. |
| Día 6, 16:30 | Con ayuda externa se cierran sesiones, se revocan tokens y se descubre y elimina la regla de reenvío. Fin del acceso. |
| Día 8 | Se intenta reconstruir el alcance. Conclusión de la investigación: **"no se puede determinar"**. |

## Impacto

- **Confidencialidad (pilar principal afectado):** acceso de seis días a un buzón con hilos que contenían información de clientes. Alcance exacto: indeterminable.
- **Integridad (intento):** el intento de desviar un pago a proveedor no llegó a ejecutarse — el compañero lo detectó antes. Fue margen de suerte, no de control.
- **Disponibilidad:** sin impacto.
- **Confianza y obligaciones:** al no poder acotar qué datos se accedieron, FinTrack no pudo evaluar con base real sus obligaciones de notificación. La incertidumbre es un daño en sí misma.

## Causa raíz

"Un empleado hizo clic" **no** es la causa raíz — es el detonante. La pólvora estaba puesta en tres decisiones (o ausencias de decisión) previas:

1. **Una contraseña sola bastaba.** Sin segundo factor, credencial robada = cuenta tomada. El clic solo entregó lo único que hacía falta. *(→ se convierte en R1/C1)*
2. **Nada vigilaba.** Ni el login desde IP extranjera, ni la regla de reenvío recién creada generaron señal, porque no existía registro ni alerta alguna. Seis días es el tiempo que tarda la suerte, no la detección. *(→ R3/C3)*
3. **No había plan.** La respuesta se improvisó: la contención incompleta del día 6 (contraseña sin sesiones) le regaló horas extra al atacante, y no es un error exótico — es lo que pasa sin procedimiento escrito. *(→ plan e playbook de `06-incident-response/`)*

## Qué funcionó / qué no

| Funcionó | No funcionó |
|----------|-------------|
| El compañero que notó el hilo extraño avisó de inmediato — la cultura de "avisar" existe informalmente | La detección dependió al 100% de esa suerte: seis días de ventana |
| Se pidió ayuda externa el mismo día en vez de insistir a ciegas | Contención incompleta: contraseña cambiada, sesiones y tokens vivos |
| La regla de reenvío se encontró y eliminó antes del cierre | No hubo registro de acciones ni horas durante la respuesta: esta línea de tiempo hubo que arqueologizarla |
| | Sin registros del buzón, el alcance quedó en "no se sabe" — la peor respuesta posible |
| | El intento de fraude al proveedor estuvo a una respuesta de distancia de costar dinero real |

## Acciones correctivas

Cada acción con dueño (rol) y el riesgo del registro que cierra. Estado a la fecha de este informe:

| Acción | Dueño | Riesgo que cierra | Estado |
|--------|-------|-------------------|--------|
| MFA obligatorio (FIDO2 en TI/admin, TOTP resto) — control C1 | Responsable de infraestructura | R1 | En despliegue (semana 1 de la hoja de ruta) |
| SPF/DKIM/DMARC en modo rechazo | Responsable de infraestructura | R1 | En despliegue |
| Registros centralizados + 6 alertas (entre ellas: regla de reenvío nueva y login imposible) — control C3 | Responsable de infraestructura | R3 | Este mes |
| Plan de respuesta e playbook de phishing escritos y comunicados | Coordinador de incidentes | R1, R3 (tiempo de respuesta) | **Hecho** — `06-incident-response/` |
| Política de acceso e identidad (gestor de contraseñas, cuentas admin separadas) | Dirección + TI | R1, R6 | **Hecho** — `05-policies/politica-accesos.md` |
| Sesión de 30 min con todo el equipo: cómo reportar, por qué reportar es seguro (sin simulacros punitivos) | Dirección | R1 (detección temprana) | Este mes |
| Verificación telefónica obligatoria para todo cambio de datos bancarios de proveedores | Área financiera | Fraude BEC (proceso financiero, anotado fuera de alcance en R#) | Este mes |

**Criterio de cierre del postmortem:** cuando C1 y C3 estén operativos, se repite el escenario en simulacro (correo de prueba + regla de reenvío): la alerta debe sonar en minutos y la contención seguir el playbook. Ahí — no antes — este incidente queda cerrado.
