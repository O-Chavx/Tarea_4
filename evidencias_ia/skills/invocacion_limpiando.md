# Transcripción: invocacion_limpiando

Archivo original: `invocacion_limpiando.jsonl`

## Pedido del usuario (argumento de `claude -p`)

/limpiando-enaho 2024

**Sesión:** modelo `claude-sonnet-5-5` · MCP: ['bcrp connected']

### → Herramienta `Bash`

```json
{
 "command": "ls datos salidas 2>&1; cat salidas/validacion.txt 2>&1"
}
```

<details><summary>Resultado</summary>

```
datos:
966-Modulo02.zip
966-Modulo34.zip
catalogo_bcrp.csv
enaho01-2024-200.dta
peru_departamental_simple.geojson
referencia_sesion16.csv
sumaria-2024.dta

salidas:
brechas_enaho.csv
cifras_bcrp.json
cifras_bcrp_comparacion.json
indicadores.csv
plan.md
scripts
validacion.txt
verificacion.md
Validación ENAHO 2024 (código INEI 966)

Hogares en la sumaria:               33,691
Filas en el módulo 200:             117,721
Residentes habituales:              108,664
Población expandida (facpob07):      34,147,768

Pobreza, sumaria (factor07 × mieperho):    27.58 %   (INEI: 27.6 %)
Pobreza extrema, sumaria:                   5.50 %   (INEI: 5.5 %)
Pobreza urbana, sumaria:                   24.81 %   (referencia: 24.8 %)
Pobreza rural, sumaria:                    39.30 %   (referencia: 39.3 %)
Pobreza, base de personas (facpob07):      27.53 %
Pobreza extrema, base de personas:          5.50 %

[OK] Llaves de hogar únicas en la sumaria
[OK] Llaves de persona únicas
[OK] La unión no perdió ni duplicó filas
[OK] Residentes habituales = 108,664
[OK] Pobreza (sumaria) a menos de 0.1 pts de 27.6 %
[OK] Pobreza extrema (sumaria) a menos de 0.1 pts de 5.5 %
[OK] Pobreza urbana a menos de 0.2 pts de 24.8 %
[OK] Pobreza rural a menos de 0.2 pts de 39.3 %
[OK] Suma de facpob07 entre 33 y 35 millones (población del Perú)
[OK] 25 departamentos con dato

Resultado: VALIDADA
```
</details>

## Claude

Existen los dos zip y la validación dice `VALIDADA`, pero `salidas/personas_2024.parquet` no está. Por eso corro la limpieza.

### → Herramienta `Bash`

```json
{
 "command": "uv run /home/user/tarea_4/.claude/skills/limpiando-enaho/scripts/limpiar.py --anio 2024 2>&1 | tail -30; ls -la salidas/*.parquet; grep -E \"Resultado\" salidas/validacion.txt"
}
```

<details><summary>Resultado</summary>

```
warning: The `UV_NATIVE_TLS` environment variable is deprecated and will be removed in a future release. Use `UV_SYSTEM_CERTS` instead.
Validación ENAHO 2024 (código INEI 966)

Hogares en la sumaria:               33,691
Filas en el módulo 200:             117,721
Residentes habituales:              108,664
Población expandida (facpob07):      34,147,768

Pobreza, sumaria (factor07 × mieperho):    27.58 %   (INEI: 27.6 %)
Pobreza extrema, sumaria:                   5.50 %   (INEI: 5.5 %)
Pobreza urbana, sumaria:                   24.81 %   (referencia: 24.8 %)
Pobreza rural, sumaria:                    39.30 %   (referencia: 39.3 %)
Pobreza, base de personas (facpob07):      27.53 %
Pobreza extrema, base de personas:          5.50 %

[OK] Llaves de hogar únicas en la sumaria
[OK] Llaves de persona únicas
[OK] La unión no perdió ni duplicó filas
[OK] Residentes habituales = 108,664
[OK] Pobreza (sumaria) a menos de 0.1 pts de 27.6 %
[OK] Pobreza extrema (sumaria) a menos de 0.1 pts de 5.5 %
[OK] Pobreza urbana a menos de 0.2 pts de 24.8 %
[OK] Pobreza rural a menos de 0.2 pts de 39.3 %
[OK] Suma de facpob07 entre 33 y 35 millones (población del Perú)
[OK] 25 departamentos con dato

Resultado: VALIDADA

Base guardada en salidas/personas_2024.parquet (108,664 filas, 26 columnas)
Indicadores en salidas/indicadores.csv
-rw-r--r-- 1 root root 645982 Nov  7 16:31 salidas/personas_2024.parquet
Resultado: VALIDADA
```
</details>

## Claude

La limpieza de la ENAHO 2024 (código INEI 966) quedó lista y `salidas/validacion.txt` dice `VALIDADA`.

La base está en `salidas/personas_2024.parquet`, con 108,664 residentes habituales y 26 columnas. Faltaba el parquet, así que corrí `limpiar.py --anio 2024` sin cambiar el filtro, las llaves ni los pesos. También se actualizó `salidas/indicadores.csv`.

**Pobreza frente a la cifra del INEI**

| Indicador | Resultado | Referencia |
|---|---|---|
| Pobreza, método sumaria (`factor07 × mieperho`) | 27.58 % | 27.6 % |
| Pobreza extrema, método sumaria | 5.50 % | 5.5 % |
| Pobreza urbana, método sumaria | 24.81 % | 24.8 % |
| Pobreza rural, método sumaria | 39.30 % | 39.3 % |
| Pobreza, base de personas (`facpob07`) | 27.53 % | — |
| Pobreza extrema, base de personas (`facpob07`) | 5.50 % | — |

- **Chequeos:** pasaron los 10 chequeos. La pobreza difiere del INEI en menos de 0.1 puntos.
- **Población:** `facpob07` suma 34,147,768 personas, y los 25 departamentos tienen dato.
- **Qué método usar:** la pobreza de la base de personas (27.53 %) no es idéntica a la de la sumaria (27.58 %). Para una cifra que deba coincidir con la publicada por el INEI, hay que usar el método de la sumaria. Para cualquier otro cálculo sobre la base, usa `facpob07`, nunca conteos de encuestas.

---
**Fin:** success · 3 turnos · 16 s · US$ 0.07

