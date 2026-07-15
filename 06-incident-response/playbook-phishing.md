# Playbook — Incidente de phishing

Procedimiento paso a paso para el incidente más probable (R1). Es la versión con nombres y clics del plan general: en pánico no se piensa, se sigue la lista.

**Cuándo se activa:** alguien reporta un correo sospechoso, admite haber ingresado credenciales en una página dudosa, o suena una alerta de C3 (login imposible / regla de reenvío nueva). **Ante la duda, se activa** — falsa alarma cuesta 30 minutos; alarma ignorada costó 6 días.

## Pasos

1. **Quien detecta** avisa al canal #seguridad o llama al coordinador. Anota la hora. No borra el correo, no apaga nada, no "prueba el enlace para confirmar".
2. **Coordinador** pregunta lo único que cambia todo: *¿se ingresaron credenciales (o se aprobó un MFA)?*
   - **No** → SEV-3: saltar al paso 8 (limpieza de la campaña) y registrar.
   - **Sí / no sabe** → SEV-2: continuar en orden.
3. **Contener la cuenta (investigación técnica), en este orden:**
   a. Cerrar todas las sesiones activas de la cuenta desde la consola de administración de la suite.
   b. Restablecer la contraseña.
   c. Revocar app passwords y tokens/OAuth grants de la cuenta.
   d. Verificar que el MFA de la cuenta no fue cambiado (¿dispositivo nuevo registrado?).
4. **Buscar persistencia:** revisar reglas de reenvío/filtros creados recientemente en la cuenta y delegaciones de buzón. Es la primera maniobra del atacante — en abril lo fue.
5. **Preservar evidencia ANTES de limpiar:** exportar el correo con encabezados completos, capturar la URL de la página falsa, guardar los registros de acceso de la cuenta (IPs, horas). Va todo al registro del escriba.
6. **Dimensionar el alcance:** desde los registros de acceso — ¿desde cuándo entró el atacante? ¿qué leyó/envió? ¿Se usó la misma contraseña en otros sistemas? Si esa cuenta tiene acceso a consola de nube, GitHub o terceros: rotar esas credenciales también.
7. **Evaluar escalado (coordinador):** ¿hay correos con datos de clientes en el buzón? ¿indicios de fraude a proveedores o clientes? → aplicar los disparadores del plan general (dirección + abogado).
8. **Limpiar la campaña:** buscar el mismo correo en las otras 44 bandejas (búsqueda de administrador) y purgarlo; bloquear remitente/dominio; reportar la URL de phishing al navegador y al hosting.
9. **Avisar al equipo** con instrucciones concretas ("si recibiste un correo de X con asunto Y, repórtalo; no hagas clic") — sin incluir el enlace vivo, sin señalar a nadie.
10. **Cerrar:** el coordinador confirma en los registros que no hay actividad residual (48 h de vigilancia reforzada sobre la cuenta) y agenda el postmortem.

## Qué NO hacer

- **No borrar el correo original** antes del paso 5 — es la evidencia.
- **No coordinar la respuesta desde el buzón comprometido** — el atacante lee. Canal alternativo del plan.
- **No cambiar solo la contraseña.** Sin cerrar sesiones y revocar tokens (3a y 3c), el atacante sigue dentro con la sesión vieja. Fue exactamente el error de abril.
- **No responder al atacante** ni "probar" sus enlaces desde equipos de la empresa.
- **No culpar a quien cayó — ni en el canal, ni en broma.** La próxima detección temprana depende de que reportar sea seguro. Quien reporta rápido ayudó, punto.
- **No anunciar el incidente a clientes o en público** por iniciativa de TI: eso es decisión de dirección con el abogado (disparadores del plan).

## Después

Para el postmortem (plantilla en `07-postmortem/`), el escriba entrega:

- Línea de tiempo con horas: recepción, clic, contención de cada paso.
- Alcance establecido: qué se accedió, qué no se pudo determinar y por qué.
- IoCs: remitente, dominio, URL, IPs de acceso.
- Qué funcionó y qué estorbó del propio playbook — el playbook también se corrige.
- Acciones correctivas propuestas, cada una con dueño y R# que cierra.
