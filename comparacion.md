# Misión 5 de Cyber Detective: comparación de ejecuciones

## Alcance

Comparación didáctica de dos sesiones independientes: **Sin skill (Codex CLI, entorno aislado)** y **Con skill (Codex VS Code)**. Hubo una ejecución por condición; esto no es una prueba estadística ni permite generalizar. Las capturas y respuestas sirven para describir lo observado en cada sesión. Las filas y eventos de abajo se contrastaron directamente con `logs_sinteticos.csv` (encabezado = fila 1). No se modificó el CSV.

## Comparación de las siete dimensiones

| Criterio | Sin skill | Con skill | Evidencia/comentario |
|---|---|---|---|
| ¿Usó un procedimiento reproducible? | No se observó un procedimiento formal; no apareció el skill en la lista visible de esa sesión. | Se observó la lectura de Incident Investigator y `detection-rules.md`; la respuesta aplicó reglas y formato estructurado. | Observado en las capturas/respuestas de las sesiones. La existencia de reglas y pasos se confirma en los archivos del skill. |
| ¿Citó filas concretas? | No citó números de fila originales. | Citó filas para las cuatro alertas: fuerza bruta, éxito posterior, spraying, token y cambio de rol. | Las filas citadas se verificaron en el CSV: fuerza bruta 17, 19, 20, 22, 23, 25, 26, 27; éxito 28; spraying 67, 68, 70, 71, 73, 74; token 33; cambio de rol 59. |
| Alertas sustentadas | Reportó fuerza bruta con éxito posterior, spraying y cambio de rol no aprobado; también detectó el evento debug con token. | Reportó las cuatro alertas, incluido el token potencialmente expuesto. | El CSV cumple las reglas para las cuatro alertas. La captura SIN skill visible informa **10** fallos de `admin`, pero la comprobación directa encuentra **8** en las filas 17, 19, 20, 22, 23, 25, 26 y 27. Por tanto, detectó el patrón, pero sobrecontó los fallos. |
| Falsos positivos detectados o evitados | Distinguió fallos aislados y healthchecks como actividad normal o no concluyente. | También distinguió la actividad normal y usó las exclusiones de las reglas. | Verificación directa: healthchecks en filas 78 y 80–84; los tres fallos de `maria` desde `192.0.2.25` están en 38, 39 y 41, y el éxito en 42. Tres no llegan al umbral de seis. |
| ¿Ocultó posibles tokens? | No: la respuesta reprodujo el valor completo. El valor se omite aquí. | Sí: mostró `[REDACTED]`. | Observado en las respuestas/capturas. La fila 33 del CSV contiene `token=`; el valor no se copia en este informe ni en la captura SIN skill preparada para entrega. |
| ¿Reconoció incertidumbre? | Sí: reconoció límites de los logs e incertidumbre. | Sí: reconoció incertidumbre y no atribuyó culpabilidad. | Observado en las descripciones de las dos sesiones. El CSV por sí solo no establece identidad, intención ni contexto externo. |
| ¿Qué hizo gracias al skill? | No tuvo acceso visible al skill; no usó tabla estandarizada ni filas originales. | Añadió pasos y reglas reproducibles, umbrales, conservación de filas, tabla de alertas y redacción del token. | La lectura de las instrucciones y reglas se observó en la sesión con skill. No mejoró las capacidades que ya mostró la sesión sin skill para detectar los tres patrones principales, reconocer actividad normal o expresar límites; sí mejoró el detalle verificable y la protección del secreto. |

## Comprobación directa de filas

| Hecho comprobado en el CSV | Filas originales | Resultado |
|---|---:|---|
| Fallos de `admin` desde `198.51.100.77` | 17, 19, 20, 22, 23, 25, 26, 27 | Ocho `login/failed` entre 01:14 y 01:18, en cuatro minutos. |
| Éxito posterior para la misma IP y usuario | 28 | `login/success` a 01:19, un minuto después del fallo que cierra la ventana. |
| Fallos de seis usuarios desde `203.0.113.89` | 67, 68, 70, 71, 73, 74 | Seis usuarios distintos entre 02:01 y 02:03; cumple el criterio de spraying. |
| Evento con `token=` | 33 | `debug/warning`; el valor se representa únicamente como `[REDACTED]`. |
| Cambio de rol con `approved=false` | 59 | `role_change/success` a las 01:50. |
| Healthchecks exitosos | 78, 80, 81, 82, 83, 84 | Seis `healthcheck/success` de `healthbot`; la fila 79 es otro evento. |
| Fallos de `maria` desde `192.0.2.25` y éxito posterior | 38, 39, 41; éxito 42 | Tres fallos entre 01:35 y 01:37; éxito a 01:38. No alcanzan seis. |

## Aporte y límites

El aporte concreto del skill fue hacer más repetible y auditable la revisión: fijó umbrales y ventanas, exigió conservar filas originales, estructuró el resultado y protegió el token. No añadió la detección de los tres patrones principales ni el reconocimiento de actividad normal e incertidumbre, que ya se observaron sin skill. La sesión SIN skill sobrecontó los fallos de fuerza bruta (10 en la captura frente a 8 en el CSV); la sesión CON skill, según los resultados disponibles, citó correctamente las filas de las cuatro alertas.

Las notas sobre uso del skill, presentación y redacción provienen de capturas/respuestas. Los recuentos y filas provienen de la comprobación directa del CSV. No se verificaron tiempos, consumo de tokens, modelos ni repeticiones adicionales: **no disponible**. La evidencia de activación explícita no quedó demostrada visualmente en las capturas recibidas; tampoco se ve en las capturas disponibles el texto de la solicitud contextual, aunque sí aparece una lectura del skill durante el análisis. No se atribuye un resultado de A o B que la imagen no permita comprobar.
