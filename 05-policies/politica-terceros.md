# Política de acceso de terceros

**Mitiga:** R4 — Compromiso a través de terceros
**Responsable:** Responsable de infraestructura (TI), con la dirección para contratos
**Aplica a:** todo tercero con acceso a datos o sistemas de FinTrack (agregador bancario, banco socio, SaaS, contratistas); antes de conceder el acceso y durante toda su vigencia
**Última revisión:** 2026-07-14

## Propósito

Que FinTrack sepa en todo momento qué le dio a cada tercero, que sea lo mínimo necesario, y que pueda cortarlo en minutos. Cuando el tercero sufra su propia brecha — Evolve Bank 2024, Salesloft Drift 2025 — la diferencia entre un susto y una crisis es tener esta información antes, no buscarla durante (R4).

## Regla

1. Ningún tercero recibe acceso a datos o sistemas sin registrarse antes en el inventario de terceros: quién es, qué datos ve, qué scopes/permisos tiene, quién lo aprobó, contacto de seguridad del tercero y fecha de última rotación de credenciales.
2. Todo acceso concedido es el mínimo que el servicio necesita para funcionar. Si un scope amplio es la única opción del proveedor, se documenta como excepción.
3. Las llaves de API y tokens de terceros se rotan cada trimestre, y de inmediato si el tercero anuncia un incidente.
4. Todo contrato nuevo con acceso a datos de clientes incluye: notificación de incidentes de seguridad en un máximo de 72 horas y compromiso de protección de datos por escrito (DPA o equivalente).
5. Cada trimestre, junto con la revisión de accesos internos, se revisa el inventario: integraciones sin uso se revocan; el acceso muerto es superficie de ataque gratis para el atacante.
6. Si un tercero del inventario anuncia una brecha, se activa el plan de respuesta (`06-incident-response/`) con un objetivo inmediato: revocar/rotar todo lo concedido a ese tercero en menos de 4 horas.

## Excepciones

Un tercero que no acepte la cláusula de notificación o solo ofrezca scopes amplios puede contratarse únicamente con aprobación escrita de la dirección, constancia del riesgo aceptado en el inventario, y fecha de re-evaluación (máximo 12 meses). Con el agregador y el banco socio actuales, la renegociación se hace en la próxima renovación de contrato.

## Verificación

- El inventario de terceros vive versionado en el repositorio interno: cada cambio queda en el historial.
- La revisión trimestral queda registrada con fecha, revisor y accesos revocados.
- Métrica de éxito: cero integraciones activas fuera del inventario (se verifica contrastando los tokens/llaves vivos en la consola contra la tabla).
