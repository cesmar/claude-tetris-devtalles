---
name: clima-local
description: Obtiene el clima actual y el pronóstico de Lima, Perú por defecto (o de otra ciudad si se indica) usando wttr.in, sin API key. Úsalo cuando el usuario pregunte por el clima, la temperatura, si va a llover, el pronóstico o "cómo está el tiempo".
---

# Clima local

Consulta el clima con `curl` contra [wttr.in](https://wttr.in), que no requiere API key. La ubicación por defecto es **Lima, Perú**; solo cambia si el usuario pide otra ciudad.

## Pasos

1. Determina la ubicación:
   - Si el usuario menciona una ciudad, úsala (reemplaza espacios por `+`, ej. `Buenos+Aires`).
   - Si no, usa por defecto **Lima, Perú** (`Lima,Peru`). No uses geolocalización por IP.
2. Obtén los datos en JSON y en español:

   ```bash
   curl -s "https://wttr.in/<CIUDAD>?format=j1&lang=es"
   ```

   Para una vista rápida en una línea:

   ```bash
   curl -s "https://wttr.in/<CIUDAD>?format=%l:+%c+%t+(sensación+%f)+humedad+%h+viento+%w&lang=es"
   ```

3. Del JSON extrae:
   - `nearest_area[0]`: ciudad y país (`areaName`, `country`).
   - `current_condition[0]`: `temp_C`, `FeelsLikeC`, `humidity`, `windspeedKmph`, `winddir16Point`, `lang_es[0].value` (descripción), `precipMM`.
   - `weather[0..2]`: `date`, `mintempC`, `maxtempC`, y `hourly[].chanceofrain` para probabilidad de lluvia.
4. Responde **siempre en español**, de forma breve:
   - Ubicación consultada (Lima, Perú salvo que se haya pedido otra).
   - Clima actual: temperatura, sensación térmica, condición, humedad y viento.
   - Pronóstico de hoy y los próximos 2 días (mín/máx y probabilidad de lluvia).
   - Una recomendación corta si aplica (paraguas, abrigo, etc.).

## Notas

- Unidades métricas (°C, km/h, mm).
- En Windows PowerShell usa `curl.exe` en lugar de `curl` (alias de `Invoke-WebRequest`).
- Si la petición falla o expira, informa el error y sugiere reintentar o indicar la ciudad explícitamente. No inventes datos.
