# 03 — Registro de Riesgos

**Decisión que permite tomar este documento:** en qué orden gastar el esfuerzo limitado del equipo.

> Regla: un control sin riesgo asociado no entra al informe. Una política
> sin un riesgo que la exija, tampoco. Seis riesgos bien argumentados valen
> más que veinte listados.

---

## Resumen

| ID | Riesgo | Prob. | Impacto | Nivel | Control |
|----|--------|-------|---------|-------|---------|
| R1 | Phishing y robo de credenciales de empleados | Alta | Alto | **Crítico** | C1 |
| R2 | Toma de cuentas de clientes (credential stuffing) | Alta | Medio | **Alto** | C2 |
| R3 | Ceguera operativa: sin registros ni alertas | Alta | Alto | **Crítico** | C3 |
| R4 | Compromiso a través de terceros | Media | Alto | **Alto** | C4 |
| R5 | Ransomware y pérdida de respaldos | Media | Alto | **Alto** | C5 |
| R6 | Accesos internos excesivos y cuentas huérfanas | Media | Alto | **Alto** | C6 |

---

## R1 · Phishing y robo de credenciales de empleados

| Campo | Valor |
|-------|-------|
| **Descripción** | Un empleado entrega sus credenciales en una página falsa; el atacante entra al correo corporativo y pivota desde ahí (reset de contraseñas, fraude a proveedores, lectura de datos de clientes). |
| **Activos** | A4 (credenciales) → A1, A2, A3 por pivote |
| **Propiedad violada (CID)** | Confidencialidad (e Integridad si altera pagos) |
| **Probabilidad** | **Alta** — ya ocurrió hace tres meses, y nada ha cambiado desde entonces. La campaña contra clientes de Snowflake (2024) demostró que credencial robada + sin MFA = brecha, a escala industrial. |
| **Impacto** | **Alto** — seis días de acceso sin poder reconstruir qué se leyó. El correo es la llave de recuperación de casi todos los demás sistemas. |
| **Nivel** | **Crítico** |
| **Escenario concreto** | Contabilidad recibe una "factura actualizada" del proveedor de siempre. La página pide iniciar sesión. Veinte minutos después, el atacante lee todos los hilos con clientes y crea una regla que le reenvía cualquier correo con la palabra "pago". |
| **Control** | C1 — MFA resistente a phishing + endurecimiento del correo |

**Nota de análisis:** el riesgo no es "que alguien haga clic" — con 45 personas recibiendo correo a diario, alguien siempre hará clic. El riesgo es que **un clic baste**. Eso es una decisión de arquitectura de autenticación, no un fallo humano.

---

## R2 · Toma de cuentas de clientes (credential stuffing)

| Campo | Valor |
|-------|-------|
| **Descripción** | Atacantes prueban a escala contraseñas filtradas de otros servicios contra el login de FinTrack. Las cuentas que abren tienen bancos vinculados y pueden programar transferencias. |
| **Activos** | A2 (motor de transferencias), A1 (datos del cliente tomado) |
| **Propiedad violada (CID)** | Integridad (dinero movido) y Confidencialidad |
| **Probabilidad** | **Alta** — es el ataque más barato que existe: listas públicas + herramientas de catálogo. 23andMe (2023–2024) perdió datos de ~7M de usuarios exactamente así. El login de FinTrack hoy no limita intentos ni exige segundo factor. |
| **Impacto** | **Medio** — afecta cuentas individuales, no la plataforma entera; pero cada caso es dinero real de un cliente y un reclamo público. A escala (cientos de cuentas), escala a Alto. |
| **Nivel** | **Alto** |
| **Escenario concreto** | Un cliente usó la misma contraseña en FinTrack y en una tienda online filtrada en 2024. Un martes a las 3 a. m., un bot la prueba, entra, y programa una transferencia a una cuenta mula. El cliente se entera por su banco. FinTrack se entera por Twitter. |
| **Control** | C2 — defensa del login de clientes (MFA por pasos, límites de intentos) |

**Nota de análisis:** FinTrack no puede impedir que sus clientes reutilicen contraseñas. Puede — y debe — diseñar el login asumiendo que **ya** las reutilizaron.

---

## R3 · Ceguera operativa: sin registros ni alertas

| Campo | Valor |
|-------|-------|
| **Descripción** | No existen registros centralizados ni alertas. Un acceso indebido a cualquier activo no genera señal; la detección depende de la suerte (un compañero que "nota algo raro"). |
| **Activos** | Transversal — amplifica todos los demás riesgos |
| **Propiedad violada (CID)** | Las tres: sin visibilidad no se protege ninguna |
| **Probabilidad** | **Alta** — no es "puede pasar": es el estado actual. Ya costó seis días de acceso sin detección y un cierre de incidente sin respuestas. |
| **Impacto** | **Alto** — convierte incidentes de minutos en incidentes de días, y hace imposible cumplir cualquier obligación de notificación con datos reales. "No sabemos qué pasó" es la peor frase posible ante clientes y reguladores. |
| **Nivel** | **Crítico** |
| **Escenario concreto** | El del incidente real: seis días de acceso al correo, y al cerrar la investigación la única conclusión honesta fue "no se pudo determinar el alcance". No había qué mirar. |
| **Control** | C3 — registros centralizados + alertas de alta señal |

**Nota de análisis:** R3 es un riesgo raro: no es un ataque, es una **condición** que abarata todos los ataques. Por eso su control (C3) es de los primeros aunque no "detenga" a nadie: convierte al resto del registro en algo medible.

---

## R4 · Compromiso a través de terceros

| Campo | Valor |
|-------|-------|
| **Descripción** | El agregador bancario, el banco socio o una integración SaaS sufre su propia brecha, y los datos o tokens que FinTrack le confió quedan expuestos. FinTrack hereda el incidente sin haber sido atacada. |
| **Activos** | A5 (integraciones), A1 (datos compartidos con terceros) |
| **Propiedad violada (CID)** | Confidencialidad (y Disponibilidad si el tercero cae) |
| **Probabilidad** | **Media** — no depende de FinTrack, pero los precedentes sobran: Evolve Bank (2024) expuso datos de clientes de las fintechs que dependían de él; Salesloft Drift (2025) convirtió tokens OAuth robados en acceso masivo a cientos de empresas. |
| **Impacto** | **Alto** — los datos bancarios de los clientes viven también en el agregador. Una brecha ahí es indistinguible, para el cliente, de una brecha de FinTrack. |
| **Nivel** | **Alto** |
| **Escenario concreto** | FinTrack se entera por un comunicado de prensa de que su agregador fue comprometido. Nadie sabe qué scopes tenían los tokens concedidos, ni dónde está el inventario de qué datos se comparten. Las primeras 48 horas se van en averiguar qué se le dio al tercero, no en responder. |
| **Control** | C4 — inventario y mínimo privilegio para accesos de terceros |

**Nota de análisis:** este riesgo no se puede eliminar — FinTrack **es** su cadena de terceros; sin agregador ni banco socio no hay producto. Se administra: saber qué se compartió, limitarlo al mínimo y poder cortarlo rápido.

---

## R5 · Ransomware y pérdida de respaldos

| Campo | Valor |
|-------|-------|
| **Descripción** | Un atacante con acceso a la consola de nube cifra o borra la infraestructura **y los respaldos**, que hoy viven en la misma cuenta. La plataforma queda fuera de servicio sin camino de recuperación. |
| **Activos** | A3 (plataforma), respaldos |
| **Propiedad violada (CID)** | Disponibilidad |
| **Probabilidad** | **Media** — requiere un paso más que R1 (llegar del correo a la consola), pero ese paso es corto: el correo restablece contraseñas. Los afiliados de ransomware compran exactamente ese tipo de acceso. |
| **Impacto** | **Alto** — Change Healthcare (2024) mostró el techo: semanas de caída por una credencial sin MFA. Para FinTrack, días sin plataforma significa clientes que no pueden ver su dinero — y no vuelven. Sin respaldos fuera del alcance del atacante, "restaurar" no es una opción. |
| **Nivel** | **Alto** |
| **Escenario concreto** | Viernes 6 p. m.: las instancias empiezan a apagarse. La consola muestra los snapshots borrándose uno a uno. El único respaldo sobreviviente está en la laptop de un dev, tiene cuatro meses y nadie ha probado nunca restaurarlo. |
| **Control** | C5 — respaldos inmutables fuera de la cuenta principal + prueba de restauración |

**Nota de análisis:** un respaldo que se puede borrar con las mismas credenciales que el original no es un respaldo: es una segunda copia del problema. Y un respaldo que nunca se restauró es una hipótesis, no un plan.

---

## R6 · Accesos internos excesivos y cuentas huérfanas

| Campo | Valor |
|-------|-------|
| **Descripción** | Las 4 personas de TI comparten accesos amplios a todo; no hay proceso de baja (offboarding) ni revisión periódica. Cuentas de exempleados y tokens antiguos siguen vivos. Cualquier credencial interna comprometida — o vendida — abre la casa entera. |
| **Activos** | A1, A2, A3 — todo lo que la consola administra |
| **Propiedad violada (CID)** | Las tres, según qué haga quien entra |
| **Probabilidad** | **Media** — con 45 empleados, el insider malicioso es improbable; pero la cuenta huérfana y el token olvidado son casi inevitables sin proceso. Coinbase (2025) mostró que el acceso interno amplio tiene mercado: a su soporte tercerizado lo sobornaron. |
| **Impacto** | **Alto** — el acceso interno no pasa por el balanceador ni deja el rastro de un ataque externo. Es la diferencia entre una brecha y una brecha silenciosa. |
| **Nivel** | **Alto** |
| **Escenario concreto** | Un dev que salió de la empresa en enero conserva su llave SSH y su acceso a GitHub. En julio, su laptop personal — sin cifrar — se vende usada con la llave dentro. Nadie en FinTrack puede decir hoy cuántas llaves así existen. |
| **Control** | C6 — ciclo de vida de identidades y mínimo privilegio |

**Nota de análisis:** este riesgo crece solo. Cada mes sin proceso de baja añade cuentas huérfanas; cada proyecto nuevo añade tokens. Es el único riesgo del registro cuya probabilidad **sube** con el tiempo si no se toca.

---

## Argumento de priorización

El orden no es el del índice — es este:

1. **R1 y R3 primero, juntos.** R1 porque ya se materializó y el vector sigue abierto; R3 porque fue lo que convirtió ese incidente en seis días de caja negra. Son además los más baratos de cerrar (C1 y C3 usan capacidades que FinTrack ya paga en su suite de correo y su nube). Cerrarlos cambia la pregunta de "¿nos atacaron?" a "sabemos si nos atacaron".
2. **R5 tercero.** Es la red de seguridad: si todo lo demás falla, los respaldos inmutables son la diferencia entre un mal fin de semana y el fin de la empresa. Activarlos es rápido; probarlos, un mes.
3. **R6 y R4 después.** Son riesgos de proceso, no de tecnología: inventarios, revisiones, bajas. Importantes, pero requieren disciplina sostenida más que instalación, y su probabilidad es media, no alta.
4. **R2 al final del trimestre — no porque importe menos, sino porque cuesta más.** Exige trabajo de desarrollo en el producto (MFA por pasos, límites de intentos). Mientras tanto, C3 da visibilidad parcial sobre logins anómalos de clientes.

La regla detrás del orden: **primero lo que ya se demostró explotable, luego la red de seguridad, luego el proceso, y el trabajo de producto se planifica en vez de improvisarse.**

---

## Riesgos evaluados y descartados

| Riesgo considerado | Por qué no entra al registro |
|--------------------|------------------------------|
| DDoS volumétrico | El proveedor de nube absorbe el volumen base. FinTrack no tiene perfil de extorsión por DDoS. Reevaluar si la empresa gana perfil público. |
| Actores estatales / APT | Sin valor estratégico. El costo de defensa supera el valor de la empresa. Ya excluido en el alcance. |
| Día cero contra infraestructura | Nadie quema un 0-day donde entra un correo falso. La higiene de parches (rutina de TI, no un riesgo top-6) cubre los n-days. |
| Cadena de suministro de dependencias (npm) | Real — 2025 lo probó — pero detrás de los seis en probabilidad×impacto para FinTrack. Mitigación barata anotada en C6 (lockfiles, alertas de dependencias), sin categoría propia. |
| Robo físico en oficina | No hay activos críticos en la oficina salvo laptops, y las laptops se cubren en R6 (cifrado de disco, baja de accesos). |
| Fraude interno financiero (contabilidad) | Es un riesgo de control financiero (doble firma, conciliación), no de seguridad de la información. Se recomienda tratarlo, pero en el proceso contable — fuera de este alcance. |

---

## Siguiente documento

`04-security-controls/security-controls.md` — un control por riesgo, con alternativas y descartes.
