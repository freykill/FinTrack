# Política de respaldos y recuperación

**Mitiga:** R5 — Ransomware y pérdida de respaldos
**Responsable:** Responsable de infraestructura (TI)
**Aplica a:** base de datos de clientes, configuración de infraestructura y repositorios de código; permanente
**Última revisión:** 2026-07-14

## Propósito

Que exista siempre una copia de los datos que un atacante con control total de la cuenta principal de nube **no pueda borrar ni cifrar**, y que restaurarla sea un procedimiento probado, no una esperanza (R5).

## Regla

1. La base de datos de clientes se respalda automáticamente cada día. La configuración de infraestructura, en cada cambio.
2. Una copia diaria se replica a la cuenta de nube separada de respaldos, con inmutabilidad (object lock) de 30 días. Las credenciales de esa cuenta no existen en ningún sistema de producción y las custodian dos personas designadas.
3. Retención: 30 diarias, 12 mensuales.
4. El primer viernes de cada mes se restaura el respaldo más reciente en un entorno aislado. Se verifica integridad de datos y se mide el tiempo total. Resultado y tiempo se registran.
5. Si una restauración de prueba falla, se trata como incidente (ver `06-incident-response/`): la empresa está sin red de seguridad hasta resolverlo.
6. Nadie borra respaldos manualmente. Las excepciones (requerimiento legal de supresión de datos) siguen el camino de excepciones.

## Excepciones

Cualquier supresión de datos dentro de respaldos (p. ej., derecho de supresión de un cliente) la aprueba por escrito la dirección con revisión legal, y se documenta qué se suprimió, cuándo y por orden de quién. Si una ventana técnica impide la restauración de prueba mensual, se reprograma dentro del mismo mes y se deja constancia — no se salta.

## Verificación

- El registro de restauraciones de prueba (fecha, resultado, tiempo medido, quién la ejecutó) se revisa en la reunión mensual de TI.
- Una alerta automática (control C3) avisa si la replicación a la cuenta de respaldos falla o si alguien intenta modificar la configuración de inmutabilidad.
- Métrica de éxito: 12 restauraciones de prueba exitosas al año; ninguna brecha de replicación mayor a 24 h sin investigar.
