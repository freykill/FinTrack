# 05 — Políticas mínimas viables

Cada política existe porque un riesgo la exige. Una política por archivo. La estructura común está en `PLANTILLA-politica.md`.

## Mapeo política → riesgo

| Política | Archivo | Mitiga |
|----------|---------|--------|
| Acceso e identidad | `politica-accesos.md` | R1, R6 |
| Respaldos y recuperación | `politica-respaldos.md` | R5 |
| Acceso de terceros | `politica-terceros.md` | R4 |
| Registro y monitoreo | `politica-registro-monitoreo.md` | R3 |

R2 (toma de cuentas de clientes) no tiene política: su mitigación es un control técnico dentro del producto (C2 — MFA por pasos y límites de intentos). Escribirle una "política" a los clientes no cambia su comportamiento; cambiar el login, sí.

## Lo que NO se escribe

Políticas consideradas y descartadas porque ningún riesgo del registro las exige — el relleno normativo diluye a las que sí importan:

| Política descartada | Por qué |
|---------------------|---------|
| Uso aceptable genérica | No cierra ningún R#. Las conductas que importan (credenciales, reportes de incidentes) ya viven en políticas con riesgo asociado. |
| Escritorio limpio | No hay activos críticos en papel ni oficina con público. Teatro de cumplimiento. |
| Política de contraseñas standalone | Las reglas de credenciales que importan están en `politica-accesos.md`, junto al MFA que las hace secundarias. Un documento aparte de complejidad de contraseñas es la reliquia que el MFA vino a jubilar. |
| BYOD integral | El riesgo real (laptop sin cifrar, acceso sin baja) ya se cubre en accesos. Un marco BYOD completo es para empresas con flotas que FinTrack no tiene. |
| Clasificación de la información en 4 niveles | Con 45 personas, la distinción operativa es binaria: datos de clientes o no. Cuatro niveles generan burocracia sin decisión asociada. |
