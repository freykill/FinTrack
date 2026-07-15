# 04 — Controles de Seguridad Propuestos

**Decisión que permite tomar este documento:** qué implementa FinTrack, en qué orden, y qué se descarta a conciencia.

> Regla: cada control cita el riesgo R# que mitiga. Por cada control, las
> opciones consideradas CON su pro y su contra. Si una opción no tiene contra,
> no la pensaste bien.

---

## C1 · MFA resistente a phishing + endurecimiento del correo — mitiga R1

**Objetivo:** que una credencial robada, sola, no sirva para nada. Cerrar el vector exacto del incidente de abril.

| Opción | Pro | Contra |
|--------|-----|--------|
| A. Códigos por SMS | Cero fricción de adopción; todos tienen teléfono | Vulnerable a SIM swapping y phishing en tiempo real. Es el MFA que los atacantes ya saben saltar |
| B. App TOTP (códigos de 6 dígitos) | Gratis, incluida en la suite actual, familiar | Sigue siendo phishable: una página falsa puede pedir el código y usarlo en vivo |
| C. Passkeys / llaves FIDO2 | Resistente a phishing por diseño: la llave no funciona en el dominio falso | Costo de llaves físicas (~USD 30 c/u) o curva de adopción de passkeys; recuperación de cuenta requiere proceso |

> **Recomendación:** C para las cuentas que abren todo — las 4 de TI y las cuentas de administración — y B para el resto de los 45 empleados, aplicado como obligatorio desde la suite de correo. Combinado con: SPF, DKIM y DMARC en modo de rechazo (hoy el dominio de FinTrack se puede suplantar), y alerta automática de reglas de reenvío nuevas.
> El costo total en llaves es el de una laptop. No es la mejor solución en abstracto — es la que 4 personas pueden desplegar en una semana sin proyecto.
> **Cuándo:** esta semana.

---

## C2 · Defensa del login de clientes — mitiga R2

**Objetivo:** que la contraseña reutilizada de un cliente no baste para mover su dinero.

| Opción | Pro | Contra |
|--------|-----|--------|
| A. MFA obligatorio para los 28.000 clientes | Máxima protección uniforme | Fricción real: abandono en el registro y avalancha de tickets de soporte para 1 persona de soporte. Castiga al 100% por el riesgo del 5% |
| B. MFA por pasos (step-up): segundo factor solo al vincular banco, programar transferencia o cambiar datos + límite de intentos + verificación contra contraseñas filtradas | Protege las acciones que mueven dinero sin fricción en el uso diario; el rate limiting frena el stuffing a escala | Requiere trabajo de desarrollo (semanas de los 2 devs); la consulta de saldo queda tras un solo factor |
| C. WAF con gestión de bots delante del balanceador | Frena el stuffing sin tocar código | Costo mensual permanente, requiere afinación continua que nadie tiene tiempo de hacer, y no protege contra el atacante lento y manual |

> **Recomendación:** B. Las transferencias son donde el riesgo se vuelve dinero (R2 viola Integridad); ahí va el segundo factor. El límite de intentos en el balanceador es una tarde de trabajo y mata el ataque a escala. C queda en la hoja de ruta de 12 meses si el volumen de bots lo justifica con datos de C3.
> **Cuándo:** este trimestre (es trabajo de producto; se planifica, no se improvisa).

---

## C3 · Registros centralizados y alertas de alta señal — mitiga R3

**Objetivo:** que la próxima vez la pregunta "¿qué pasó?" tenga respuesta. Detección en horas, no en días-descubiertos-por-suerte.

| Opción | Pro | Contra |
|--------|-----|--------|
| A. SIEM comercial (Splunk, QRadar) | Potencia y correlación de verdad | Precio de licencia + exige un analista que lo mire. Compraría un Ferrari para un equipo sin conductor |
| B. Registros nativos de la nube y la suite de correo, centralizados, retención 12 meses + 6 alertas de alta señal | Casi todo ya está pagado, solo hay que activarlo; 6 alertas bien elegidas caben en el día a día de 4 personas | Sin correlación sofisticada; las alertas van a un canal, no a un SOC. Detecta lo conocido, no lo sutil |
| C. MDR (detección y respuesta gestionada 24/7) | Ojos expertos permanentes sin contratar personal | Costo anual significativo, y sin los básicos (logs activados, inventario) el MDR vigila una casa sin cerraduras |

> **Recomendación:** B ahora. Las seis alertas: (1) inicio de sesión imposible por geografía/hora, (2) creación de reglas de reenvío de correo, (3) exportación masiva desde la BD, (4) cambios de permisos IAM, (5) borrado o modificación de respaldos, (6) inicio de sesión de cuenta administrativa fuera de horario. Cada una habría sonado durante el incidente de abril. C (MDR) se reevalúa a 12 meses, cuando exista qué entregarle.
> **Cuándo:** este mes.

---

## C4 · Inventario y mínimo privilegio para terceros — mitiga R4

**Objetivo:** saber exactamente qué se le dio a cada tercero, reducirlo al mínimo, y poder cortarlo en minutos.

| Opción | Pro | Contra |
|--------|-----|--------|
| A. Plataforma GRC / gestión de riesgo de proveedores | Cuestionarios, scoring y seguimiento automatizados | Costo y burocracia dimensionados para cientos de proveedores; FinTrack tiene menos de diez |
| B. Inventario versionado en el repositorio + scopes mínimos + rotación trimestral de llaves y tokens + cláusula de notificación de incidentes en contratos | Gratis, auditable en el historial de git, y proporcional: diez terceros caben en una tabla | Depende de disciplina trimestral; sin herramienta que persiga a nadie |

> **Recomendación:** B. Una tabla con: tercero, qué datos ve, qué scopes tiene, quién es el contacto de seguridad, fecha de última rotación. Al firmar con terceros nuevos: exigir notificación de incidentes en 72 h. El caso Salesloft Drift (2025) se sufrió menos en las empresas que sabían qué tokens habían concedido y los revocaron en horas.
> **Cuándo:** este mes (el inventario, esta semana; las rotaciones, ciclo trimestral).

---

## C5 · Respaldos inmutables + prueba de restauración — mitiga R5

**Objetivo:** que exista una copia que el atacante con la consola **no puede borrar**, y la certeza práctica de que se puede restaurar.

| Opción | Pro | Contra |
|--------|-----|--------|
| A. Snapshots automáticos en la misma cuenta de nube | Ya casi existe; un clic | Quien compromete la consola borra el respaldo junto con el original. Es la situación actual con otro nombre |
| B. Copia diaria a una cuenta de nube separada con inmutabilidad (object lock) y credenciales que producción no conoce | Sobrevive el compromiso total de la cuenta principal; costo de almacenamiento marginal | Configuración inicial delicada (cross-account); restaurar es más lento que un snapshot local |
| C. Proveedor externo de backup (SaaS) | Simplicidad de gestión | Costo mensual y un tercero más en el inventario de R4 — ironía no menor |

> **Recomendación:** B, manteniendo A para recuperación rápida del día a día (los snapshots locales resuelven el "borré una tabla"; la cuenta separada resuelve el ransomware). Más la regla que no se negocia: **restauración de prueba mensual, documentada, con tiempo medido.** Un respaldo sin prueba de restauración es una hipótesis.
> **Cuándo:** esta semana (activar copia inmutable); este mes (primera restauración de prueba).

---

## C6 · Ciclo de vida de identidades y mínimo privilegio — mitiga R6

**Objetivo:** que dar de baja a una persona apague **todos** sus accesos el mismo día, y que nadie tenga más acceso del que su trabajo exige.

| Opción | Pro | Contra |
|--------|-----|--------|
| A. Checklist manual de alta/baja + revisión trimestral de accesos | Gratis, empieza mañana | Depende de disciplina; una baja olvidada = cuenta huérfana |
| B. SSO con la suite de correo como proveedor de identidad en todo SaaS que lo soporte | Apagar una cuenta apaga todo lo conectado; menos contraseñas que robar | El "impuesto SSO": varios SaaS lo cobran en planes caros; nunca cubre el 100% (llaves SSH, tokens de API) |
| C. Proveedor de identidad dedicado (Okta y similares) | El estándar enterprise | Sobredimensionado: costo y administración pensados para cientos de empleados y decenas de apps |

> **Recomendación:** B donde el plan lo permita sin sobrecosto + A como red obligatoria para todo lo que quede fuera (llaves SSH, tokens, la consola de nube). Incluye: cifrado de disco en todas las laptops, roles separados de administración (nadie opera con la cuenta admin a diario), y — de la nota en el modelo de amenazas — lockfiles y alertas de dependencias en GitHub como higiene de cadena de suministro sin costo.
> **Cuándo:** este trimestre (la checklist y la primera revisión de accesos, este mes).

---

## Resumen: qué se hace y cuándo

| Cuándo | Acciones | Riesgos que atacan |
|--------|----------|--------------------|
| Esta semana | MFA obligatorio (llaves FIDO2 en TI/admin, TOTP resto) · DMARC en rechazo · copia inmutable de respaldos en cuenta separada · inventario de terceros iniciado | R1, R5, R4 |
| Este mes | Registros centralizados + 6 alertas · primera restauración de prueba · checklist de alta/baja + primera revisión de accesos · rotación inicial de llaves de terceros | R3, R5, R6, R4 |
| Este trimestre | MFA por pasos y límite de intentos en el login de clientes · SSO en los SaaS que lo permitan · cifrado de disco verificado en todas las laptops | R2, R6 |

---

## Lo que deliberadamente NO recomendamos

Un informe gana credibilidad por lo que descarta. Nada de esto es malo — es desproporcionado para 4 personas de TI y este presupuesto, hoy:

| Descartado | Por qué |
|-----------|---------|
| SIEM comercial | Sin analista que lo mire, es un gasto que genera culpa en vez de detección. C3-opción B da el 80% del valor con lo ya pagado. |
| SOC propio o MDR 24/7 (hoy) | Vigilancia experta sobre una casa sin cerraduras. Primero C1–C6; MDR se reevalúa a 12 meses. |
| WAF con gestión de bots (hoy) | Costo permanente + afinación continua para un problema que el rate limiting resuelve en su mayor parte. Entra si C3 muestra volumen de bots que lo justifique. |
| Prueba de penetración inmediata | Pagaría por confirmar lo que ya sabemos: que la puerta está abierta. Tiene fecha: a 12 meses, cuando haya controles que valga la pena probar. |
| Certificación ISO 27001 / SOC 2 (hoy) | Certificar un proceso que aún no existe. Se reevalúa cuando un cliente enterprise la exija — entonces será inversión comercial, no teatro. |
| DLP (prevención de fuga de datos) enterprise | Requiere clasificación de datos madura y genera falsos positivos que 4 personas no pueden triar. |
| Microsegmentación / Zero Trust integral | Es una arquitectura destino, no un proyecto trimestral. Las piezas que sí valen ya están dentro: MFA fuerte, mínimo privilegio, inventario. |
| Capacitación anti-phishing con simulacros punitivos | Los simulacros que castigan destruyen lo que más se necesita: que la gente reporte rápido y sin miedo. La formación va dentro del playbook y el postmortem, en cultura sin culpables. |

---

## Siguiente documento

`05-policies/` — las políticas que estos controles exigen para sostenerse en el tiempo.
