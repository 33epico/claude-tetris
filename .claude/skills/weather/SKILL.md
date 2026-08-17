---
name: weather
description: >
  Consulta temperatura y estado del clima actual de una ciudad (por defecto Algete).
  Usar cuando el usuario pida "el tiempo", "el clima", "la temperatura", o invoque /weather.
---

# Weather

Consulta rápida del clima actual de una ciudad.

## Uso

- `/weather` — sin argumentos: usa ciudad por defecto **Algete**.
- `/weather <ciudad>` — consulta esa ciudad en su lugar.

## Pasos

1. Llamar `WebSearch` con query: `<ciudad> weather now temperature current conditions`.
   - Si el resultado trae datos claramente inconsistentes entre fuentes (fechas/cachés mezcladas), decirlo explícito en vez de inventar un número.
2. Extraer de los resultados: temperatura actual (°C, convertir si viene en °F), estado del cielo (soleado/nublado/lluvia/etc.), y si está disponible: sensación térmica, viento, humedad.
3. Responder en castellano, breve, con estos datos.
4. Cerrar con lista de fuentes en markdown (URLs de los resultados usados).

## Notas

- No hay API key ni endpoint fijo: todo sale de búsqueda web en vivo, así que la fuente puede variar entre llamadas.
- Si se usa dentro de un `/loop`, cada disparo repite estos mismos pasos.
