# Política de acceso e identidad

**Mitiga:** R1 — Phishing y robo de credenciales de empleados · R6 — Accesos internos excesivos y cuentas huérfanas
**Responsable:** Responsable de infraestructura (TI)
**Aplica a:** los 45 empleados y todo contratista con acceso a sistemas de FinTrack; siempre
**Última revisión:** 2026-07-14

## Propósito

Que una credencial robada, sola, no abra nada (R1), y que ninguna persona conserve accesos que su trabajo ya no exige (R6). Esta política existe porque en abril una sola contraseña bastó para seis días de acceso sin detección.

## Regla

1. Toda cuenta corporativa usa MFA. Las cuentas de TI y de administración usan llave FIDO2 o passkey; el resto, app TOTP como mínimo. SMS no cuenta como segundo factor.
2. Toda contraseña corporativa se genera y guarda en el gestor de contraseñas de la empresa. Nadie reutiliza una contraseña entre servicios ni la comparte por chat o correo.
3. Las cuentas de administración se usan solo para administrar. El trabajo diario se hace con la cuenta personal estándar.
4. Al ingresar una persona, TI concede accesos desde la checklist de alta: solo los sistemas que su rol lista, nada "por si acaso".
5. Al salir una persona, TI ejecuta la checklist de baja **el mismo día**: suspender cuenta de correo/SSO, revocar sesiones y tokens, rotar llaves compartidas que conociera, recuperar equipo.
6. Cada trimestre, el responsable de infraestructura revisa la lista completa de cuentas y accesos (correo, nube, GitHub, SaaS) y da de baja lo que no corresponda a una persona activa con necesidad vigente.
7. Toda laptop de la empresa tiene el disco cifrado y bloqueo automático de pantalla.

## Excepciones

Si un sistema no soporta MFA o SSO, el acceso requiere aprobación escrita del responsable de infraestructura, se registra en el inventario de excepciones con fecha de revisión, y la contraseña de ese sistema vive solo en el gestor. No hay excepciones verbales ni permanentes: toda excepción tiene dueño y fecha de caducidad.

## Verificación

- El panel de la suite de correo reporta cobertura de MFA: debe ser 100% de cuentas activas. Lo revisa TI el primer lunes de cada mes.
- La revisión trimestral de accesos queda registrada en un documento con fecha, revisor y bajas ejecutadas.
- Cada baja de personal genera su checklist firmada; Personas no cierra la salida sin ella.
