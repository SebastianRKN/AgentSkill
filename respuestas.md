# Misión 6: respuestas

## 1. Skill frente a plugin

`incident-investigator` es un **Agent Skill** porque su `SKILL.md` explica cuándo usarlo y da instrucciones repetibles para revisar el CSV: validar columnas y fechas, aplicar reglas, conservar filas originales, proteger tokens y presentar límites. `references/detection-rules.md` aporta criterios concretos. No es por sí mismo un plugin: no tiene manifiesto de plugin, servidor MCP ni hooks.

Para distribuirlo como plugin, podríamos empaquetar el skill y sus referencias en una carpeta `skills/` y añadir un manifiesto `plugin.json` que identifique el paquete. Eso organiza e instala componentes; no convierte las instrucciones en herramientas nuevas. Un comando o script podría automatizar una revisión local si se añadiera, pero no forma parte de lo que construimos. Los hooks ejecutan acciones en eventos configurados del runtime; tampoco existen en este proyecto. Un servidor MCP expondría herramientas o conectaría con datos/servicios externos; no hace falta para el flujo actual, que trabaja con un CSV local, y no implementamos uno. [OpenAI Developers: Skills](https://developers.openai.com/plugins/concepts/skills) y [arquitectura de plugins](https://developers.openai.com/plugins/concepts/plugins) describen estas diferencias y el empaquetado.

## 2. Ralph Loop

Usaría un ciclo de hasta **tres pasadas**, sin instalar nada: analizar, comprobar y corregir el informe si falla alguna comprobación.

1. Validaría el esquema y los timestamps; aplicaría literalmente las reglas de `detection-rules.md` y guardaría por cada alerta los números de fila originales. Comprobaría el umbral de seis fallos para fuerza bruta, cinco usuarios distintos para spraying, el éxito posterior dentro de cinco minutos, `token=` y `approved=false`.
2. Revisaría cada coincidencia contra esas filas del CSV y recalcularía ventanas y recuentos. Comprobaría también exclusiones: los `healthcheck` no cuentan como fallos de login y tres fallos no alcanzan el umbral de fuerza bruta. Corregiría filas o conclusiones que no coincidan con los registros.
3. Haría una revisión final del informe: cada alerta debe tener evidencia suficiente; las conclusiones deben conservar la incertidumbre; y ningún valor tras `token=` puede aparecer sin redactar como `[REDACTED]`. Si detectara un error, lo corregiría y repetiría las verificaciones afectadas.

**Criterio de parada:** terminar antes si todas las comprobaciones pasan; si queda un fallo, corregir y repetir mientras quede una pasada. Tras la tercera pasada, parar y dejar explícito cualquier punto no verificado, sin inventar evidencia. Es un método propuesto; no significa que se hayan ejecutado tres iteraciones ni añade mediciones a las pruebas existentes.
