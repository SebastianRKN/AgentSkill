# Hallazgos de investigación

**Fuente:** `logs_sinteticos.csv` (análisis local; no se modificó). Las filas citadas cuentan el encabezado como fila 1. Las severidades son orientativas. Estas coincidencias son alertas justificadas por los registros, no conclusiones forenses.

## Validación de entrada

El encabezado contiene una sola vez cada columna requerida: `timestamp`, `ip`, `user`, `event`, `result` y `detail`; no hay columnas adicionales. Se pudieron interpretar los 93 timestamps y todos incluyen la misma zona `-05:00`; no se excluyeron filas por fecha inválida. Los eventos se revisaron cronológicamente, conservando sus filas originales. No se observó una ambigüedad de duplicados que impida aplicar las reglas: aunque hay filas repetidas en marca de tiempo, son registros diferenciados y la regla cuenta eventos coincidentes.

## Alertas

| Alerta | Severidad orientativa | Regla | Timestamp | IP | Usuario | Filas/evidencia | Limitaciones |
|---|---|---|---|---|---|---|---|
| Fuerza bruta probable; éxito posterior observado | Alta | 6 o más `login/failed` para el mismo usuario e IP en ventana inclusiva de hasta 5 minutos | Fallos de 01:14 a 01:18; `login/success` posterior a 01:19, dentro de los 5 minutos siguientes al último fallo | `198.51.100.77` | `admin` | Fallos en filas 17, 19, 20, 22, 23, 25, 26 y 27 (8 eventos); éxito posterior en fila 28 | El éxito posterior no confirma compromiso. Los registros no verifican identidad, autorización ni contexto fuera del CSV. La IP es de un rango de documentación y no debe tratarse como infraestructura real. |
| Password spraying probable | Alta | `login/failed` desde una IP hacia al menos 5 usuarios distintos en hasta 5 minutos | 02:01–02:03 | `203.0.113.89` | 6 usuarios: ana, carlos, luisa, diego, maria y sofia | Filas 67, 68, 70, 71, 73 y 74; un fallo por cada usuario indicado | La regla señala un patrón, no confirma automatización ni acceso. La IP pertenece a un rango de documentación; no es evidencia de infraestructura real. |
| Token potencialmente expuesto en registro | Media | `detail` contiene `token=` | 01:27 | `192.0.2.44` | `api_service` | Fila 33: evento `debug`, resultado `warning`; el valor del token se omite: `token=[REDACTED]` | El registro no demuestra que el valor sea válido, que se haya usado o que haya salido del entorno. No se reproduce el valor. |
| Cambio de rol no autorizado según el registro | Alta | `role_change/success` y `approved=false` en `detail` | 01:50 | `203.0.113.42` | `guest` | Fila 59: `role_change`, `success`, `role=user->admin; approved=false` | Refleja únicamente el indicador de aprobación registrado; no prueba el contexto completo, la identidad ni culpabilidad. La IP pertenece a un rango de documentación. |

## Fallos que no alcanzan el umbral

Se observaron tres fallos en `192.0.2.25` para `maria` en las filas 38, 39 y 41 (01:35, 01:36 y 01:37). Aunque caben en una ventana de cinco minutos, son solo tres; no alcanzan los seis requeridos para fuerza bruta probable. La fila 42 registra después un éxito, pero tampoco cambia el umbral. Esto no se clasifica como fuerza bruta con la regla indicada.

También hay tres fallos tempranos aislados por combinaciones distintas: fila 3 (`192.0.2.11`/`sofia`), fila 7 (`192.0.2.12`/`luisa`) y fila 14 (`192.0.2.10`/`carlos`). No se combinan porque fuerza bruta requiere la misma IP y el mismo usuario. Otros `login/failed` aislados tampoco alcanzan por sí solos el umbral.

## Exclusiones y límites

Las filas 78 y 80–84 corresponden a `healthcheck/success`, no a `login/failed`; por ello no cuentan para fuerza bruta ni password spraying. El análisis se limita a coincidencias de las reglas del skill en este CSV sintético. No determina si hubo intrusión, uso real de credenciales, autorización válida o actividad externa.

## Validación manual contra filas originales

Se contrastaron directamente las líneas originales del CSV, contando el encabezado como fila 1. Por ejemplo, la fila 17 contiene `2026-11-05T01:14:00-05:00,198.51.100.77,admin,login,failed`; las filas 19, 20, 22, 23, 25, 26 y 27 muestran el mismo IP/usuario y `login/failed` hasta 01:18. La fila 28 es `login/success` a 01:19. Esto verifica manualmente tanto la combinación de ocho fallos como el éxito posterior citado.

## Evidencia observable de consulta del skill

En esta ejecución se leyó el contenido de `.agents/skills/incident-investigator/SKILL.md` y de `.agents/skills/incident-investigator/references/detection-rules.md` antes de analizar el CSV. La salida de lectura mostró el procedimiento (validación del esquema, timestamps, conservación de filas, protección de secretos y formato) y las reglas aplicadas (umbrales de 6 fallos y 5 usuarios, token, escalada, falsos positivos y exclusión de `healthcheck`). Esta es la evidencia observable disponible en la sesión; no se afirma una traza independiente de activación automática.

