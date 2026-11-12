# Evidencias de las pruebas

- `control_negativo.png`: captura de conteo de eventos por hora que indica que no se investigaron ni clasificaron incidentes. Corresponde a la prueba C.
- `comparacion_con_skill.png`: captura de análisis con skill; se ve la lectura del skill y el inicio del resultado estructurado.
- `detalle_con_skill.png`: continuación del resultado con las cuatro alertas y filas citadas.
- `comparacion_sin_skill.png`: copia de entrega de la comparación SIN skill. Se cubrió el fragmento de depuración que contenía un token para protegerlo. La captura conserva el conteo de 10 fallos que hizo esa sesión; al contrastarlo con el CSV, el conteo correcto es 8.
- `activacioncontextual.png`: muestra una lectura del skill y actividad de análisis sobre el CSV; la solicitud del usuario no aparece, así que no permite comprobar que la solicitud fuera contextual ni que omitiera nombrar el skill.
- `evidencia lectura agent skill.png`: muestra comandos que leen el archivo del skill, pero no una invocación explícita por parte del usuario. No demuestra por sí sola la prueba A.

La captura original SIN skill se conserva localmente como `sinskill.png`, pero no se incluye para entrega porque contiene un valor de token sin redactar. No se alteró `logs_sinteticos.csv`. Las capturas disponibles no bastan para verificar visualmente las pruebas A y B completas; no se les atribuye evidencia faltante.
