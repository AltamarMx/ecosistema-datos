# Python para la ciencia de datos moderna
### Ecosistema reproducible e integración de la IA generativa

**Duración:** 2 h · **Formato:** Quarto (revealjs)
**Antecedente:** [Del vibe coding a la ciencia reproducible](https://altamarmx.github.io/vibecoding-reproducible/) (spec-driven: `PLAN.md` + `AGENTS.md`) · repo local: `~/vibecoding-reproducible`

**Tesis:** si la IA ya escribe código, ¿para qué aprender a programar y seguir aprendiendo herramientas nuevas? Respuesta: porque **el agente trabaja con lo que tú sabes pedirle y solo tú puedes verificar lo que hace**. Cada bloque deja una pista (🧭) y el cierre la responde.

**Caso:** 2 años de datos ClimaLab (minutal) recorren toda la sesión:
`CSV → Parquet → marimo → src/ → paquete → barrido paramétrico → agente → MCP`

🖐 = el público lo sigue en su máquina · 👀 = demo, solo observan · ♻️ = material reutilizable de la charla anterior · 🧭 = pista para la tesis

---

## Cronología

| min | Bloque | Modo |
|----:|--------|:----:|
| 0–7 | 0. Motivación: la reproducibilidad, medida | 👀 |
| 7–12 | 1. El problema y la pregunta | 👀 |
| 12–27 | 2. uv y ambientes virtuales | 🖐 |
| 27–42 | 3. Versionado: qué es git y por qué importa el diff | 🖐 |
| 42–57 | 4. Narrativa computacional: de Jupyter a marimo | 🖐 |
| 57–65 | 5. Parquet | 🖐 |
| 65–70 | *Pausa* | |
| 70–85 | 6. De `src/` al paquete instalable y la exploración paramétrica | 🖐 |
| 85–103 | 7. IA generativa: del chat al agente, el harness, nube y local, spec-driven | 👀 |
| 103–110 | 8. MCP | 👀 |
| 110–120 | 9. Cierre: por qué seguir aprendiendo a programar | |

---

## 0. Motivación: la reproducibilidad, medida (7′, 5 diapos) 👀
Fuente: `referencias/evidencia-reproducibilidad.md`. Cifras verificadas contra los originales (19 oct 2026).

1. **Qué es (y qué no).** Reproducibilidad = mismos datos + mismo código → mismo resultado; replicabilidad = datos nuevos (NASEM 2019). Su recomendación 4-1: compartir datos, métodos **y la descripción del entorno computacional**.
2. **El embudo** (Samuel y Mietchen, *GigaScience* 2024): 27,271 notebooks → 22,578 Python → 15,817 declaran dependencias → 10,388 se instalan → 1,203 corren → **879 dan el mismo resultado** (3.2%).
3. **No es un campo, es un patrón:**
   - Notebooks Jupyter en GitHub: 24% corre, **4%** reproduce (Pimentel et al. 2019).
   - Scripts R en Harvard Dataverse: **74%** falla al ejecutarse (Trisovic et al. 2022).
   - *Science*, tras exigir código: 44% de artefactos obtenibles, **26%** reproducible (Stodden et al. 2018).
   - Macroeconomía: **33%** sin ayuda de los autores (Chang y Li 2015).
   - Sin intervención humana: ~5–35%, en distintas disciplinas y décadas.
4. **Las causas tienen nombre… y herramienta** (diapo puente al resto de la charla):
   | Causa documentada | Bloque |
   |---|---|
   | Dependencias no declaradas, "module not found" | uv, `uv.lock` (2) |
   | Sin historia, sin control de versiones | git (3) |
   | Estado oculto, celdas fuera de orden | marimo (4) |
   | Rutas absolutas, sin orden de ejecución | estructura + `uv run` (2, 6) |
   | Preprocesamiento no documentado | narrativa + `PLAN.md` (4, 7) |
   | 47% sin formación formal en programación (AlNoamany y Borghi 2018) | 🧭 la tesis |
5. **Cerca de casa:** ML para fallas en HVAC: 65 estudios, **2** compartían código (uno roto) (Mukhtar et al. 2025). En México no hay medición cuantitativa: es un vacío… y una oportunidad.

## 1. El problema y la pregunta (5′) 👀
- ♻️ La carpeta caótica: `Untitled27.ipynb`, `analisis_final_v3_AHORASI.py`, `datos_limpios_DEFINITIVO.csv`: así se ve, desde dentro, un proyecto que no se reproduce (el 96 % de Pimentel).
- Reproducible = otra persona (o tú en 6 meses) obtiene lo mismo con un comando.
- Vibe coding (Karpathy) sirve para lo desechable; la ciencia necesita verificación.
- 🧭 La pregunta que queda abierta: *si la IA programa, ¿para qué aprender a programar?*

## 2. uv y ambientes virtuales (15′) 🖐
- Una herramienta: versiones de Python, entornos, dependencias, ejecución.
- `uv init` · `uv add` · `uv run` · `uv.lock` → el entorno es un archivo versionado.
- Scripts autocontenidos con dependencias en línea (PEP 723).
- ♻️ Estructura propuesta: `data/` (crudos intocables) · `src/` (funciones) · `tests/` · `notebooks/` (narrativa) · `PLAN.md` · `AGENTS.md`.
- Ambiente virtual: el refri compartido vs. tu lonchera.
- 🖐 Instalar uv, `uv init --no-package`, `uv add polars marimo`, correr algo con `uv run`.
- 🧭 Un agente sin instrucciones hace `pip install` global y `requirements.txt`: repite lo que más vio al entrenarse. Pedirle `uv` exige saber que existe.
- Puente: `uv init` ya creó un repositorio git… ¿qué es eso?

## 3. Versionado: qué es git y por qué importa el diff (15′) 🖐
- ♻️ El meme: `tesis_final_por_fin_eso_espero.docx`. Todos versionamos a mano.
- El problema: copias con nombres, sin autor, fecha ni motivo; no se sabe qué cambió entre una y otra.
- git: un solo archivo, toda su historia. *Commit* = foto con autor, fecha y mensaje; punto de retorno.
- git (software libre) ≠ GitHub (plataforma: respaldo y colaboración; gratis vía GitHub Education).
- **El diff**: líneas que salen (`-`) y entran (`+`). Con agentes es *la* unidad de revisión.
- ♻️ El caso `sum()` → `mean()`: corre, el número es plausible, solo el diff lo delata.
- El diff solo sirve si el archivo es texto legible: `.docx` y `.ipynb` no lo son → motivo del bloque 4.
- 🧭 El diff solo sirve si sabes leer código: la IA escribe, tú revisas.
- 🖐 `git add` · `git commit` · editar · `git diff` · `git log` · volver atrás.

## 4. Narrativa computacional: de Jupyter a marimo (15′) 🖐
- Recordar el 24% / 4% de Pimentel. Qué resolvió Jupyter y qué dejó pendiente: estado oculto, ejecución fuera de orden, `.ipynb` (JSON + outputs) ilegible en el diff, dependencias no declaradas, no importable.
- marimo: grafo reactivo, archivo `.py` puro (diff limpio), corre como script o como app.
- IA integrada en el editor (nube o local).
- ♻️ Quarto sigue siendo la capa de publicación (artículo, reporte, estas diapositivas).
- 🧭 La herramienta cambia el tipo de error que es posible cometer: estar al día no es moda, es eliminar clases enteras de errores.
- 🖐 Abrir un notebook marimo, mover un slider, ver la reactividad; comparar `git diff` de un `.ipynb` vs. un marimo `.py`.

## 5. Parquet (8′) 🖐
- CSV: sin tipos, pesado, lento. Parquet: columnar, tipado, comprimido.
- Polars / DuckDB leen Parquet de forma perezosa (solo lo que se necesita).
- Datos crudos inmutables; derivados en Parquet (binario: no se versiona con git, se regenera).
- **Demo: 2 años de ClimaLab** (minutal, 11 variables, ~1 M filas). Estimado a partir de 4 meses medidos:

  | | CSV | Parquet |
  |---|---:|---:|
  | Tamaño | ~85 MB | ~21 MB |
  | Lectura pandas | ~1.4 s | ~0.07 s |
  | Lectura polars | ~0.25 s | ~0.05 s |
  | `date` al leer | `str` (hay que parsear) | `datetime` |
  | Media de `tdb` (lazy, 1 columna) | lee todo | ~0.01 s |

- 🧭 Elegir formato es una decisión técnica que nadie toma por ti: el agente usa CSV si no le dices otra cosa.
- 🖐 Con un script de `uv run` (PEP 723): generar el CSV desde el Parquet, leer ambos, comparar tamaño, tiempo y tipos; luego `scan_parquet` + filtro de fechas + 1 columna.

## 6. De `src/` al paquete instalable y la exploración paramétrica (15′) 🖐
- **Qué hay en `src/`**: el código que *define* (funciones puras, con docstring, unidades y prueba); el notebook solo *narra* y las llama.
  ```text
  src/climalab/
  ├── io.py          # leer crudos → Parquet limpio y tipado
  ├── radiacion.py   # p. ej. ghi_diaria() (la del diff sum→mean)
  └── modelo.py      # la función parametrizable del barrido
  ```
- Por qué `src/` y no sueltas junto al notebook: se importa igual desde marimo, pytest, un script o el agente; obliga a instalarlo (lo que pruebas es lo que se instala).
- Señal para sacar código del notebook: lo copiaste dos veces, o necesita una prueba.
- ♻️ Cuando `src/` crece, conviértelo en paquete: documentado, versionado, citable (EnerHabitat).
- `uv build` / instalable desde git o PyPI; versiones fijadas en el `uv.lock` de quien lo usa.
- Una función bien definida + una malla de parámetros = barrido paramétrico.
- Resultados del barrido → Parquet → exploración en marimo.
- 🧭 Decidir qué es una función, qué parámetros barrer y qué prueba la valida es *diseño*, no tecleo: ahí está el valor de saber programar.
- 🖐 `uv add` del paquete y correr un barrido pequeño.

## 7. IA generativa: del chat al agente, el harness, nube y local, spec-driven (20′) 👀
- **El modelo solo**: texto entra, texto sale. No ve tus archivos, no ejecuta nada; tú copias y pegas.
- **+ herramientas**: leer/escribir archivos, ejecutar comandos, correr pruebas.
- ♻️ **+ bucle**: propone → ejecuta → observa el resultado → corrige → repite. Eso es un **agente**.
- **El harness** (arnés): el programa que envuelve al modelo y lo vuelve agente. Decide:
  - qué herramientas tiene y con qué **permisos** (¿puede borrar? ¿pide confirmación?),
  - qué **contexto** carga (`AGENTS.md`, el plan, el repo),
  - cómo maneja la **memoria** y el contexto largo.
  - Ejemplos: Claude Code, Codex CLI, OpenCode; también el asistente de marimo.
- **Modelo ≠ harness**: ♻️ mismo harness, distinto motor — nube (capacidad) vs. local, p. ej. Qwen en MLX/Ollama (privacidad).
- ♻️ Spec-driven vs. vibe coding: `PLAN.md` = contrato firmado con un commit; `AGENTS.md` = reglas (`uv add`, `uv run`, nunca `pip install`).
- ♻️ Bucle: una sección del plan → código → render → **leer el diff** → commit.
- 🧭 Escribir `AGENTS.md` y `PLAN.md` exige saber programar *y* conocer las herramientas actuales: el agente rinde al nivel de quien lo guía.
- 👀 El agente implementa una sección del plan; leemos el diff juntos.

## 8. MCP (7′) 👀
- ♻️ Protocolo estándar para dar herramientas y datos a un agente.
- Del paquete al servidor MCP: el barrido paramétrico se vuelve una herramienta que el agente invoca.
- 🧭 El agente solo puede usar tu conocimiento si lo empaquetaste: tu código bien hecho se vuelve su herramienta.
- 👀 Preguntarle al agente algo que lo obligue a usar nuestra herramienta MCP.

## 9. Cierre: por qué seguir aprendiendo a programar (10′)
- Recapitular: uv + git + marimo/Quarto + Parquet + `src/`/paquete = ecosistema reproducible; el agente trabaja *dentro* de él.
- Responder la pregunta del bloque 1 recogiendo las 🧭.
- ♻️ **Saber programar es la licencia de conducir:**
  - **Auditar** — sin leer código no revisas lo que escribe el agente; el diff es tu instrumento.
  - **Entender** — el criterio de dominio decide si el número es plausible (`sum` vs. `mean`).
  - **Guiar** — el plan, las correcciones y la firma son humanas. *La IA no firma.*
- **Estar al día es parte del oficio:**
  - Casi todo lo de hoy (uv, marimo, Polars 1.x, MCP, agentes con harness) no existía hace ~3 años (fechas verificadas: uv feb 2024 → OpenCode mediados 2025).
  - Los modelos aprendieron del pasado: sus defaults son `pip`, `.ipynb`, CSV. **El presente lo aporta quien los guía** (`AGENTS.md`).
  - Mientras más capaz es el agente, más pesa el criterio de quien lo dirige.
- ♻️ La IA sube el **piso**; el **techo** —criterio, verificación, firma— sigue siendo humano.
- ♻️ Propuesta de curso / cómo seguir aprendiendo; preguntas.

---

## Requisitos para quien quiera seguir 🖐
- `uv` y `git` instalados; `git config` con nombre y correo.
- Clonar el repo del taller antes de empezar: `uv sync`.
