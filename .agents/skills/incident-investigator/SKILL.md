---
name: incident-investigator
description: Investiga alertas potenciales de seguridad en archivos CSV sintéticos locales con las columnas timestamp, ip, user, event, result y detail (se permiten columnas adicionales). Se activa cuando se solicita investigar, correlacionar o evaluar posibles incidentes en esos registros; no se activa por defecto para conteos, visualizaciones o estadísticas descriptivas sin investigación de incidentes, ni para datos remotos o no sintéticos.
---

# Incident Investigator

Investiga eventos potencialmente relacionados en CSV sintéticos locales. Una alerta es una coincidencia con una regla; por sí sola no confirma que haya ocurrido un incidente ni identifica quién es responsable.

## Procedimiento

1. **Limita el alcance.** Trabaja únicamente con el CSV sintético local que indique la solicitud. No uses red, servicios externos ni otros archivos como fuentes de eventos. No cambies el archivo de entrada.
2. **Valida el esquema antes de analizar.** Lee el encabezado del CSV y comprueba que existan exactamente una vez las columnas requeridas `timestamp`, `ip`, `user`, `event`, `result` y `detail`. Puede haber columnas adicionales. Si falta una requerida, está duplicada o el archivo no se puede leer como CSV, detente y explica el problema; no supongas valores.
3. **Valida timestamps.** Comprueba que cada `timestamp` de los registros pueda interpretarse como fecha y hora. Si hay valores vacíos o inválidos, informa cuántas filas y cuáles (por número de fila original), y no incluyas esos registros en ventanas temporales. Conserva la representación original del timestamp para reportar. Si la zona horaria falta o hay zonas incompatibles que impiden comparar instantes de forma fiable, declara la limitación y no afirmes una correlación temporal que dependa de ella.
4. **Consulta las reglas.** Antes de buscar coincidencias, lee [`references/detection-rules.md`](references/detection-rules.md) completo. Aplica solo sus criterios; no añadas umbrales ni inferencias propias. Si la solicitud trata de una regla o falso positivo, consulta esa referencia igualmente.
5. **Ordena y conserva procedencia.** Asigna a cada registro su número de fila original en el CSV, contando el encabezado como fila 1 (el primer registro es la fila 2). Ordena cronológicamente por `timestamp` antes de analizar ventanas y conserva el número original aunque cambie el orden. No elimines duplicados: si registros duplicados o empates hacen ambigua una interpretación, informa la ambigüedad y no inventes una regla para resolverla.
6. **Evalúa alertas.** Aplica las coincidencias exactas de `event` y `result`, los umbrales y las ventanas inclusivas definidos en la referencia. Para cada alerta, identifica la combinación de registros que satisface la regla y sus filas originales. Para fuerza bruta probable, informa por separado cualquier `login/success` posterior dentro de los cinco minutos que establezca la referencia. No mezcles criterios de reglas distintas.
7. **Protege secretos.** Si `detail` contiene `token=`, nunca copies ni muestres su valor, ni en citas ni en fragmentos de evidencia; sustitúyelo por `[REDACTED]`. Evita incluir secretos que aparezcan en otras columnas también.
8. **Separa observación e interpretación.** Describe la coincidencia como alerta potencial y atribúyela a los valores observados y a la regla aplicada. No la presentes como hecho confirmado, intrusión probada, autorización real o conducta de una persona. No atribuyas culpabilidad.
9. **Declara limitaciones.** Indica problemas de esquema o timestamps, zonas horarias, datos faltantes, ambigüedad por duplicados, límites del CSV y cualquier contexto que no pueda verificarse en esos registros. No inventes eventos ni completes datos ausentes.

## Formato estándar de resultados

Usa una fila por alerta. Si no hay coincidencias, indícalo explícitamente y no fabriques filas de alerta. La severidad es orientativa, no una conclusión sobre impacto o culpabilidad.

| Alerta | Severidad orientativa | Regla | Timestamp | IP | Usuario | Filas/evidencia | Limitaciones |
|---|---|---|---|---|---|---|---|

En **Filas/evidencia**, cita números de fila originales y resume solo los campos necesarios para justificar la coincidencia. En **Timestamp**, incluye el intervalo relevante y, cuando aplique, el evento posterior por separado. Redacta cualquier valor de token como `[REDACTED]`.
