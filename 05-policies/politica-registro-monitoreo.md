# Política de registro y monitoreo

**Mitiga:** R3 — Ceguera operativa: sin registros ni alertas
**Responsable:** Responsable de infraestructura (TI)
**Aplica a:** todos los sistemas que autentican usuarios, administran infraestructura o tocan datos de clientes; permanente
**Última revisión:** 2026-07-14

## Propósito

Que la próxima vez que alguien pregunte "¿qué pasó?", haya dónde mirar. En abril, seis días de acceso indebido terminaron en "no se pudo determinar el alcance" porque no existía registro alguno. Esta política convierte esa lección en regla (R3).

## Regla

1. Se registran, como mínimo: inicios de sesión (éxito y fallo) en correo, nube, GitHub y aplicación; acciones administrativas (cambios IAM, creación/borrado de recursos); creación de reglas de correo; exportaciones o consultas masivas a la base de datos; operaciones sobre respaldos; y las transferencias emitidas por la plataforma.
2. Los registros se centralizan en el servicio de logs de la nube, con retención de 12 meses, y protegidos contra edición (quien administra producción no puede borrar el registro de lo que hizo).
3. Las 6 alertas de alta señal del control C3 están siempre activas y llegan al canal de seguridad del equipo. Una alerta desactivada temporalmente cuenta como excepción y se documenta.
4. Toda alerta se atiende el mismo día hábil: alguien la mira, decide si es real, y deja una línea de registro (real / falso positivo / ajustar regla).
5. Los relojes de todos los sistemas se sincronizan por NTP — una línea de tiempo con relojes distintos no reconstruye nada.

## Excepciones

Si un sistema no puede enviar registros al punto central (limitación del proveedor), se documenta en el inventario de excepciones con qué registro local ofrece y cómo se consultaría en un incidente. Silenciar una alerta ruidosa requiere ajustar la regla o registrar la excepción con fecha de revisión — nunca ignorarla en silencio.

## Verificación

- Revisión mensual de TI: ¿todas las fuentes de la regla 1 siguen enviando registros? (una fuente muda es ceguera que volvió sin avisar).
- El registro de atención de alertas (regla 4) se muestrea en la revisión trimestral: toda alerta debe tener su línea de cierre.
- Métrica de éxito: en un simulacro semestral (p. ej., crear una regla de reenvío de prueba), la alerta correspondiente suena en menos de 15 minutos.
