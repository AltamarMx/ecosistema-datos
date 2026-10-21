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
| 42–57 | 4. De Jupyter a marimo | 👀 |
| 57–65 | 5. Una historia con datos, de la mano de un agente | 👀 |
| 65–70 | *¿Preguntas?* | |
| 70–85 | ~~6. De `src/` al paquete instalable y la exploración paramétrica~~ (comentado) | |
| 85–103 | ~~7. IA generativa: del chat al agente~~ (eliminado; su contenido pasó al bloque 5) | |
| 103–110 | ~~8. MCP~~ (eliminado; el concepto queda tras las preguntas) | |
| 110–120 | 9. Cierre: por qué seguir aprendiendo a programar | |

---

## 0. Motivación: la reproducibilidad, medida (7′, 5 diapos) 👀
Fuente: `referencias/evidencia-reproducibilidad.md`. Cifras verificadas contra los originales (19 oct 2026).

1. **El embudo** (Samuel y Mietchen, *GigaScience* 2024): 27,271 notebooks → 22,578 Python → 15,817 declaran dependencias → 10,388 se instalan → 1,203 corren → **879 dan el mismo resultado** (3.2%).
2. **Qué es (y qué no).** Reproducibilidad = mismos datos + mismo código → mismo resultado; replicabilidad = datos nuevos (NASEM 2019). Su recomendación 4-1: compartir datos, métodos **y la descripción del entorno computacional**.
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
- El caso de hoy: 2 años de ClimaLab y el recorrido de herramientas.
- 🖐 **Práctica adelantada (2′):** en climalab.ier.unam.mx → *Download*, cada quien elige las fechas que quiera y descarga su recorte en **Parquet**; ese archivo se usa en la práctica del bloque 3 (la app de marimo).

## 2. uv y ambientes virtuales (15′) 🖐
- Una herramienta: versiones de Python, entornos, dependencias, ejecución.
- Ambiente virtual: el refri compartido vs. tu lonchera.
- 🖐 Práctica (5′): la diapositiva solo enuncia los **objetivos**; las instrucciones son la siguiente diapositiva ("Cuatro comandos"). Al terminar: proyecto `climalab/` con `pyproject.toml`, `.python-version` y git · carpetas `data/` y `notebooks/` · marimo y matplotlib fijados en `uv.lock` · marimo abierto en el navegador.
- 🧭 Un agente sin instrucciones hace `pip install` global y `requirements.txt`: repite lo que más vio al entrenarse. Pedirle `uv` exige saber que existe.
- Cuatro comandos, uno por uno: `uv init` · `uv add` · `uv run marimo edit` · abrir la carpeta; `pyproject.toml` (lo que pides) vs. `uv.lock` (lo que obtuviste) → el entorno es un archivo versionado.
- ♻️ Estructura propuesta: `data/` (crudos intocables) · `src/` (funciones) · `tests/` · `notebooks/` (narrativa) · `PLAN.md` · `AGENTS.md`.
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
- 🖐 Práctica (6′). **Objetivo:** una gráfica interactiva con control de fechas, hecha en marimo y versionada con git. La diapositiva enuncia el objetivo; la siguiente ("La app, commit a commit") trae el código, una celda por bloque para copiarla, y las instrucciones de git.
  - `esolmet` = `pd.read_parquet("data/001_raw/ClimaLab.parquet")` (el Parquet descargado en el bloque 1; índice datetime).
  - `mo.ui.date_range` para el rango; dos subplots: `tdb` arriba, `ghi`/`dni`/`dhi` abajo.
  - Ciclo git: commit inicial → `uv add pandas pyarrow` → `git diff pyproject.toml` → commit → app en `notebooks/graficas.py` → `git add notebooks` → commit → `git log`. Los datos no se agregan a git.

## 4. De Jupyter a marimo (15′) 👀
- Recordar el 24% / 4% de Pimentel. Qué resolvió Jupyter y qué dejó pendiente: estado oculto, ejecución fuera de orden, `.ipynb` (JSON + outputs) ilegible en el diff, dependencias no declaradas, no importable.
- marimo: grafo reactivo, archivo `.py` puro (diff limpio), corre como script o como app.
- IA integrada en el editor (nube o local).
- Pregunta abierta, justo antes de la IA en el editor: **¿Cómo integramos IA en un flujo de trabajo reproducible, colaborativo y auditable?** (la responde el bloque 5).
- ♻️ Quarto sigue siendo la capa de publicación (artículo, reporte, estas diapositivas).
- **marimo: el LLM trabaja dentro del kernel** (después de la IA en el editor):
  - el asistente ve los valores en memoria (`@esolmet` le pasa columnas y tipos);
  - modos de chat Ask · Agent (edita y ejecuta celdas) · Code mode (acceso al kernel);
  - marimo pair: Claude Code, Codex y otros agentes dentro del notebook en ejecución;
  - `mo.ui.chat` con cualquier proveedor, incluido Ollama local. Adelanto del bloque 5.
- **¿Qué es Quarto?** Sistema de publicación científica y técnica, de código abierto (Posit, sucesor de R Markdown):
  - `.qmd` = Markdown + código, texto plano versionable;
  - Python, R, Julia, Observable JS; renderiza también `.ipynb`;
  - una fuente → HTML, PDF, Word, ePub, presentaciones, sitios, libros, blogs, dashboards;
  - ecuaciones, citas, referencias cruzadas, paneles de figuras, callouts;
  - `quarto preview` · `quarto render --to pdf` · `quarto publish gh-pages` (así se publica esta presentación).
- 🧭 La herramienta cambia el tipo de error que es posible cometer: estar al día no es moda, es eliminar clases enteras de errores.
- Sin práctica propia: el primer notebook marimo (la app de fechas) ya se hizo en la práctica del bloque 3.

## 5. Una historia con datos, de la mano de un agente 👀
Sustituye al antiguo bloque de Parquet y absorbe el antiguo bloque 7 (IA generativa). Demo en vivo en tres pasos, dentro de `climalab/`. Tiempos por rehacer en la cronología (antes 8′ + 18′).
1. Portada: *Vamos a contar una historia con datos de la mano de un LLM como agente*.
2. **El modelo, solo** (del antiguo bloque 7): texto entra, texto sale; no ve archivos ni ejecuta nada; tú eres el bucle.
3. **¿Qué es un agente?** Un LLM que trabaja en un bucle, con herramientas, hasta cumplir un objetivo. Diagrama del bucle (prompt/PLAN.md → propone → usa herramienta: leer, escribir, `uv run`, `quarto render` → observa → corrige). Un chat devuelve texto; un agente cambia tu proyecto. Multiplica la velocidad… y el daño si no hay reglas: por eso todo pasa por git.
4. **El harness** (del antiguo bloque 7): el programa que envuelve al modelo; decide herramientas y permisos, contexto (`AGENTS.md`, plan, repo) y memoria. Claude Code, Codex CLI, OpenCode, el asistente de marimo.
5. 👀 **Paso 1: abre la terminal** (2′): `claude` (o `codex`/`opencode`) con el prompt *"Crea un documento de Quarto en analysis/ y comienza con un hola mundo"*. Observar: pide permiso, crea el `.qmd`, lo renderiza; `git status` y `git diff`.
6. **Modelo ≠ harness: nube y local** (del antiguo bloque 7): nube = frontera, costo por uso, los datos salen; local (Qwen vía MLX u Ollama) = los datos no salen, sin costo por token, exige hardware, rinde menos. La decisión es por proyecto.
7. **Alternativa local: Ollama + OpenCode.** OpenCode necesita contexto de 64k o más; también lee `AGENTS.md`. Comandos: `ollama pull qwen3.5` · `OLLAMA_CONTEXT_LENGTH=64000 ollama serve` · `ollama launch opencode`. `ollama launch` también abre Claude Code o Codex con un modelo local: cambia el motor, no el harness.
8. 👀 **Paso 2: las reglas y el plan** (2′). Se llevan escritos, se leen y se firman con `git commit`.
   - `AGENTS.md`: solo dentro del repositorio · `data/001_raw/` de solo lectura · `uv add` y `uv run`, nunca `pip install` · Quarto en `analysis/` con el código embebido en celdas `{python}`, sin scripts sueltos · muestra el diff antes de cada commit.
   - `PLAN.md`: objetivo (la última semana: temperatura y radiación), datos, pasos (1 hola mundo ✓; 2 plotly con 2 subpaneles, última semana = 7 días hasta el último dato) y criterios (render sin errores, el diff no toca `data/001_raw/`).
   - Notas rescatadas del antiguo bloque 7: el harness carga `AGENTS.md` en cada sesión y sin él usa sus defaults de entrenamiento; el plan se escribe antes de pedir código, su commit es la firma del contrato y también es bitácora.
   - Claude Code: confirmar con `/memory` que cargó `AGENTS.md`; si no, `CLAUDE.md` con la línea `@AGENTS.md`.
9. 👀 **Paso 3: que los datos cuenten algo** (4′): prompt *"Carga el Parquet y haz una gráfica con 2 subpaneles usando plotly, para la última semana de los datos: la tdb en un panel y las componentes de la radiación (dni, ghi, dhi) en otro"*. En el diff: `uv add` (no `pip install`), código embebido en el `.qmd`, `data/001_raw/` intacto, última semana como dice `PLAN.md`. Respaldo probado en las notas (`uv add plotly jupyter` + `make_subplots(rows=2)`; `uv run quarto render analysis/index.qmd`).
10. **Vibe coding vs. spec-driven** (del antiguo bloque 7): el prompt como especificación y "Accept All" contra `PLAN.md` firmado, diff leído y commit.
11. **El bucle de trabajo** (del antiguo bloque 7, adaptado): un paso del plan → el agente escribe el `.qmd` → `uv run quarto render` → revisas la gráfica → lees el diff → `git commit`.
    - 🧭 Escribir `AGENTS.md` y `PLAN.md` exige saber programar y conocer las herramientas actuales: el agente rinde al nivel de quien lo guía.

## Tras las preguntas: paquetes para agentes y MCP (≈3′) 👀
Dos diapositivas que abren la segunda mitad, en este orden:
1. **Estrategia: paquetes que el agente usa con parámetros.** Tú escribes y pruebas la física (`temperatura_sol_aire`, la misma del bloque 6); el agente elige parámetros y combina. La función es el contrato (nombre, unidades, docstring, prueba); en el diff queda una llamada, no física nueva; instalable y versionado con uv. Ejemplo de prompt: barrer absortancia 0.2–0.8 y h 5–20 W/m²K para la última semana y graficar las horas sobre 40 °C.
2. **Explorar MCP: el concepto.** Estándar abierto para conectar aplicaciones de IA con herramientas y datos; creado por Anthropic (2024), gobernado desde dic 2025 por la Agentic AI Foundation (Linux Foundation) junto con `AGENTS.md`. Host → un cliente por servidor; el servidor ofrece *tools*, *resources* y *prompts*; el agente descubre (`tools/list`) y llama (`tools/call`); local por stdio o remoto por HTTP. Mensaje: tu paquete, envuelto en un servidor MCP, es una herramienta que cualquier agente descubre y llama. En esta charla no se construye el servidor.

## 6. De `src/` al paquete instalable y la exploración paramétrica (15′) — COMENTADO
> Fuera de la presentación: las seis diapositivas (portada → Práctica) están comentadas en `index.qmd`. Se conserva el contenido para restaurarlo.

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
