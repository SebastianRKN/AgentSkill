# Reglas de detección

Aplica estas reglas solo a registros de CSV con el esquema compatible. Para todas las ventanas, ordena primero por `timestamp`, usa intervalos inclusivos de hasta cinco minutos y conserva las filas originales del archivo contando el encabezado como fila 1. Las comparaciones de `event` y `result` son exactas y distinguen mayúsculas y minúsculas. No inventes reglas para resolver duplicados ambiguos.

## Fuerza bruta probable

Al menos 6 eventos con `event` exactamente `login` y `result` exactamente `failed`, de la misma IP y contra el mismo usuario, dentro de una ventana inclusiva de hasta 5 minutos. Emite una alerta potencial con los registros coincidentes y sus filas originales.

Informa separadamente si hay un `login` con `result` exactamente `success` de la misma IP y para el mismo usuario dentro de los 5 minutos siguientes al evento fallido que cierra la ventana. Este éxito posterior es una observación asociada, no una confirmación de compromiso.

## Password spraying probable

Eventos `login/failed` de una misma IP contra al menos 5 usuarios distintos dentro de una ventana inclusiva de hasta 5 minutos. Cuenta usuarios distintos, no eventos; cita las filas de los fallos que sustentan la alerta.

## Token potencialmente expuesto

Si `detail` contiene `token=`, genera una alerta potencial. Oculta siempre el valor del token como `[REDACTED]`; no lo reproduzcas en evidencia, citas ni resúmenes.

## Escalada no autorizada según el registro

Un evento con `event` exactamente `role_change`, `result` exactamente `success` y `approved=false` según el valor registrado en `detail` constituye una alerta potencial. No infieras culpabilidad ni que la aprobación registrada refleje el contexto completo.

## Falsos positivos y exclusiones

- Tres fallos aislados no superan el umbral de fuerza bruta.
- `healthcheck` no equivale a `login/failed` y no cuenta para las reglas de fuerza bruta o password spraying.
