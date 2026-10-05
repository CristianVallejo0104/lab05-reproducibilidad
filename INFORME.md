# Informe de los Talleres 1 y 2 · Unidad 6 · Computación en la nube

Repositorio: https://github.com/CristianVallejo0104/lab05-reproducibilidad
Etiqueta de la versión publicada: `v1.1`

## Resumen (léase primero)

**Qué hicimos.** Tomamos un análisis de viajes en Python, lo empaquetamos en una imagen de Docker y comprobamos que cualquier persona, en cualquier computador, obtiene exactamente los mismos resultados. La prueba es que las **huellas SHA-256** de los archivos coinciden.

**Cómo.** Trabajamos en dos, cada uno en archivos distintos, sobre un mismo repositorio de GitHub, usando codespaces y fusiones de Git.

**Qué aprendimos.** La reproducibilidad exige fijar cuatro cosas: **código, datos, semilla y entorno**. En el Taller 2 rompimos cada una a propósito para ver qué pasa y qué protege cada pieza.

| Sección | Contenido | Taller |
|---|---|---|
| 1 | Repositorio e integrantes | 1 |
| 2 | Reproducción de la versión 1.0 (M2) | 1 |
| 3 | Trabajo en paralelo con Git (M3) | 1 |
| 4 | Versión 1.1 y etiqueta (M4) | 2 |
| 5 | Diagnósticos: semilla, versión sin fijar, `.gitignore` (M5) | 2 |
| 6 | Semilla del proyecto (M6) | 2 |
| 7 | Conceptos clave | 1 y 2 |
| 8 | Aprendizajes | 1 y 2 |

---

## 1. Repositorio e integrantes

| Integrante | Usuario de GitHub | Rol |
|---|---|---|
| Cristian Vallejo | @CristianVallejo0104 | R1 (responsable del repositorio) y R2 (responsable de datos) |
| Juan Pablo Tibamoso | @Juanxct | R3 (responsable de análisis) |

Como el grupo es de 2 integrantes, R1 asume también el rol de R2.

**Qué hace cada archivo del repositorio:**

| Archivo | Para qué sirve |
|---|---|
| `Dockerfile` | Receta de la imagen: Python fijo, dependencias, código y punto de entrada |
| `requirements.txt` | Bibliotecas con versión exacta (`==`) |
| `src/generar_datos.py` | Genera `viajes.csv` con una semilla fija |
| `src/analisis.py` | Calcula el resumen por ciudad y el gráfico |
| `src/verificar.py` | Compara los manifiestos de dos corridas y dice si coinciden |
| `ejecutar_analisis.sh` | Punto de entrada del contenedor: genera datos y analiza |
| `reproducir.sh` | Construye la imagen, corre dos veces y verifica |
| `.gitignore` | Evita publicar datos y salidas, que se regeneran |
| `proyecto/` | Semilla del proyecto final (Taller 2) |

**Quién hizo qué en el Taller 2:**

| Parte | Responsable |
|---|---|
| Puesta a punto (M0) y reproducción (M4) | Ambos, cada uno en su codespace |
| Etiqueta `v1.1` | R1 |
| Prueba 5.1 (semilla) y 5.3 (`.gitignore`) | R1 (en grupos de 2 hace también el rol de R2) |
| Prueba 5.2 (versión sin fijar) | R3 |
| `proyecto/README.md` y `proyecto/requirements.txt` | R1 |
| `proyecto/Dockerfile` | R3 |
| `INFORME.md` | R1, con los resultados que aporta R3 |

---

## 2. Reproducción de la versión 1.0 (M2)

Los dos integrantes obtuvieron, en su propio codespace, la misma última línea de `./reproducir.sh`:

`Reproducción exacta: 3 de 3 salidas coinciden.`

Huellas SHA-256 obtenidas por **@CristianVallejo0104** y por **@Juanxct** (idénticas entre sí y con los valores de referencia):

```
154b62824a5a7f02e37a5e7a0a93e46ddd0237f2d88a57a8048800650b15a59a  resumen_ciudad.parquet
0c598115bb5676dd920eb4ada9a1be22dc6509f79aad616d795a65e337bfbb4b  resumen_ciudad.csv
568842db5a3964bdf2714a947311d127a9e35722593c2f12cba9b2ced58e74b2  tarifa_media_ciudad.png
```

**Por qué importa.** Dos personas, dos máquinas y el mismo resultado byte a byte: eso es reproducibilidad. Una huella SHA-256 identifica el contenido de un archivo, y si un solo byte cambia, la huella cambia por completo.

---

## 3. Trabajo en paralelo (M3)

Salida de `git log --oneline --graph`:

```
*   77f5ed5 (HEAD -> main, origin/main, origin/HEAD) Merge branch 'main' of https://github.com/CristianVallejo0104/lab05-reproducibilidad
|\
| * 51b7e46 Agrega Pereira, documenta integrantes y prepara imagen 1.1
* | 3c58f81 Agrega desviación estándar de la tarifa
|/
* 4a29381 Devcontainer: Ubuntu 24.04 y docker-in-docker sin moby
* ac55379 Devcontainer: base Ubuntu con Python y Docker como features
* 1bbb7bf Cambia imagen base del devcontainer (llave yarn vencida)
* c8a4173 Laboratorio 5: código, Dockerfile y dependencias fijadas
* 722c9d2 Editor de archivo nuevo con el nombre README.md y la rama main
```

**Cómo leer el grafo.** Desde `4a29381` el historial se bifurca: cada integrante hizo un cambio en paralelo y después Git los unió con la confirmación de fusión `77f5ed5`.

| Confirmación | Qué cambió | Quién |
|---|---|---|
| `51b7e46` | Agrega Pereira al generador de datos, documenta integrantes y prepara la imagen 1.1 | R1/R2 |
| `3c58f81` | Agrega la desviación estándar de la tarifa al análisis | R3 |
| `77f5ed5` | Fusión de ambas ramas | Git, al hacer `git pull` |

Conteo de confirmaciones por autor al cierre del Taller 1 (`git log --format="%an" | sort | uniq -c`):

```
3 Cristian Vallejo
5 Juanxct
```

**Push rechazado:** no hubo. Cristian publicó primero y Juan Pablo ejecutó `git pull --no-rebase --no-edit` antes de su `git push`, así que su push ya iba sobre la versión actual del remoto. Git unió los dos cambios por fusión automática (`Merge made by the 'ort' strategy`) porque se modificaron archivos distintos.

**Por qué no hubo conflicto.** Un conflicto aparece cuando dos personas cambian las mismas líneas de un mismo archivo. Aquí cada rol tocó archivos distintos, y por eso la regla de oro (`git pull --no-rebase --no-edit` antes de cada `git push`) basta.

---

## 4. Versión 1.1 (M4)

### 4.1 Puesta a punto (M0)

Los dos integrantes reconstruyeron el contenedor con *Rebuild Container* para igualar los codespaces según `.devcontainer/devcontainer.json`. Ninguno tuvo síntomas ni errores.

| # | Verificación | R1 (Cristian) | R3 (Juan Pablo) |
|---|---|---|---|
| 1 | `pwd` | `/workspaces/lab05-reproducibilidad` | `/workspaces/lab05-reproducibilidad` |
| 2 | `git pull --no-rebase --no-edit` | `Already up to date.` | Fast-forward `77f5ed5..e3d1d25` (trajo `INFORME.md` y `proyecto/requirements.txt`) |
| 3 | `git push --dry-run` | `Everything up-to-date` | `Everything up-to-date` |
| 4 | `docker run --rm hello-world` | `Hello from Docker!` | `Hello from Docker!` |
| 5 | `git log --format="%an" \| sort \| uniq -c` | Cristian Vallejo 4, Juanxct 5 | Cristian Vallejo 5, Juanxct 5 |

**Por qué el pull de R3 fue un fast-forward y no una fusión.** R3 no tenía cambios locales, así que Git solo avanzó su rama hasta el último commit de R1, sin crear confirmación de fusión. En el M3, en cambio, ambos tenían commits propios y Git tuvo que unirlos. La diferencia entre los conteos de la verificación 5 se debe a que R1 había publicado una confirmación más (`e3d1d25`, el `requirements.txt` del proyecto) antes de que R3 hiciera el pull. R1 hizo luego un fast-forward equivalente al traer el `Dockerfile` de R3 (`e3d1d25..7ecd60f`).

### 4.2 Reproducción

`./reproducir.sh` terminó con «Reproducción exacta: 3 de 3 salidas coinciden.» en los codespaces de ambos integrantes, con esta tabla:

| ciudad | n_viajes | dist. media | dur. media | tarifa media | tarifa p50 | tarifa sd |
|---|---|---|---|---|---|---|
| Barranquilla | 5126 | 5.96 | 17.89 | 16367.81 | 14438.0 | 8867.54 |
| Bogota | 22462 | 5.99 | 18.00 | 16448.88 | 14339.0 | 9188.16 |
| Bucaramanga | 2485 | 5.96 | 17.90 | 16383.86 | 14252.0 | 9010.00 |
| Cali | 7445 | 5.90 | 17.70 | 16250.58 | 14180.0 | 9101.52 |
| Medellin | 9950 | 5.97 | 17.86 | 16396.02 | 14171.0 | 9218.13 |
| Pereira | 2532 | 5.97 | 17.91 | 16402.67 | 14447.0 | 8934.61 |

Huellas SHA-256 obtenidas por **R1 y R3** (idénticas entre sí y con los valores de referencia del taller; la de `resumen_ciudad.csv` empieza por `8160729e`):

```
f3be1441539ca17baab5402f302485e799741ea4fd24960202317b4a98ce3efe  datos/viajes.csv
1343a787d52fcff776f058ce3b7057e78eaf2d9ba1a931bd8e8f67847c01081f  resumen_ciudad.parquet
8160729ebe9c436dc295431cb93e1767ce3333521210ecac635c07efd52c5874  resumen_ciudad.csv
7a75390c4f5e44653226f7eea0c53b84238810163fa3c76420af63545870cab8  tarifa_media_ciudad.png
```

**Por qué Bogotá, Medellín y Cali no cambiaron.** El generador asigna una ciudad a cada viaje comparando un número aleatorio con pesos acumulados, que funcionan como tramos del intervalo de 0 a 1. Como la semilla es la misma, la secuencia de números aleatorios es idéntica. Los tres primeros tramos (Bogotá, Medellín y Cali) no se modificaron, así que cada número cae en la misma ciudad que en la versión 1.0. Los dos últimos tramos se acortaron para dejar espacio a Pereira, y por eso solo Barranquilla y Bucaramanga cambian de valores y aparece una ciudad nueva.

**Cómo se ve la caché de Docker.** El primer `docker build` de R1 tardó 45,6 s, de los cuales 25,8 s fueron el `pip install`. El build que ejecuta `reproducir.sh` tardó 1,0 s porque cada instrucción apareció como `CACHED`: Docker reutiliza una capa mientras sus archivos no cambien. Por eso el Dockerfile copia `requirements.txt` antes que `src/`: si solo cambia el código, no se reinstalan las bibliotecas.

### 4.3 Etiqueta

Con las huellas de ambos coincidiendo, R1 publicó la etiqueta:

```
git tag -a v1.1 -m "Versión 1.1: Pereira y desviación estándar"
git push --tags
```

Verificación: en la página del repositorio, el contador *Tags* muestra 1 y, al abrirlo, aparece `v1.1`. A diferencia de una rama, la etiqueta no avanza: siempre señala la confirmación en la que se verificó la reproducibilidad.

---

## 5. Diagnósticos (M5)

### 5.1 Semilla (R1)

Al cambiar `SEMILLA` de 20260917 a 20260918 y reconstruir la imagen, `verificar.py` respondió:

```
DIFIERE   resumen_ciudad.csv
DIFIERE   resumen_ciudad.parquet
DIFIERE   tarifa_media_ciudad.png

Reproducción fallida: 0 de 3 salidas coinciden.
```

La huella de `viajes.csv` pasó de `f3be1441…` a `20d90809…`, y los números de la tabla cambiaron (por ejemplo, Bogotá pasó de 22462 a 22643 viajes).

**Explicación.** El generador es pseudoaleatorio y determinista: la semilla fija su estado inicial y de ahí sale toda la secuencia. Otra semilla produce otra secuencia completa, por lo que cambian los datos y, como todas las salidas se calculan a partir de ellos, cambian las tres. Además, SHA-256 tiene efecto avalancha: datos estadísticamente parecidos (distancia cercana a 6 km, tarifa cercana a 16 500 COP) dan huellas totalmente distintas.

**Ajuste respecto al enunciado.** El `ENTRYPOINT` de nuestra imagen (`./ejecutar_analisis.sh`) solo acepta `--filas`. Cualquier texto escrito después del nombre de la imagen se le entrega como argumento, por lo que el comando del enunciado con `python3` fue rechazado («Argumento no reconocido: python3»). Generamos `salidas_c` con `docker run` sin argumentos y ejecutamos `verificar.py` por separado; el resultado fue idéntico dentro y fuera del contenedor. Para ejecutar otro programa en la imagen hay que usar `--entrypoint`.

### 5.2 Versión sin fijar (R3)

Se cambió `numpy==2.1.3` por `numpy>=2.1` en `requirements.txt` y se construyó la imagen `lab05-viajes:prueba2`. Como en 5.1, el comando del enunciado falla por el `ENTRYPOINT`; se generaron las salidas sin argumentos en `salidas_d` y se verificó aparte:

```
IDENTICO  resumen_ciudad.csv
IDENTICO  resumen_ciudad.parquet
IDENTICO  tarifa_media_ciudad.png

Reproducción exacta: 3 de 3 salidas coinciden.
```

Versión de numpy que pip instaló, consultada dentro de la imagen con `docker run --rm --entrypoint python3 lab05-viajes:prueba2 -c "import numpy; print(numpy.__version__)"`:

```
2.5.3
```

El `manifiesto.json` de `salidas_d` registra `filas_entrada` 50000, las mismas huellas de M4 y las versiones de matplotlib 3.9.2, pandas 2.2.3, pyarrow 17.0.0 y python 3.12.15. **No registra la versión de numpy.** El enunciado decía que el manifiesto indica cuál se instaló, pero en nuestro repositorio hubo que consultarla aparte.

Después se restauró el repositorio: `git checkout -- requirements.txt`, borrado de `salidas_d` y de la imagen `prueba2`, y `git status` quedó con `working tree clean`.

**Explicación.** Hoy las huellas coinciden porque numpy 2.5.3 hace los mismos cálculos que la 2.1.3 en este análisis, pero eso es una coincidencia, no una garantía. Con `numpy>=2.1`, pip instala la versión más nueva que exista el día de la construcción, así que el resultado ya no depende solo del repositorio sino también de la fecha. Una versión futura podría cambiar la secuencia de un generador aleatorio, el redondeo en punto flotante o la compatibilidad con pandas 2.2.3; bastaría un dígito distinto para que cambie el SHA-256, o el análisis podría fallar. Además, como el manifiesto no registra numpy, si algún día los resultados difirieran no quedaría evidencia de la causa. Fijar la versión con `==` hace que cualquiera, en cualquier fecha, reconstruya exactamente el mismo entorno.

### 5.3 `.gitignore` (R1)

`git status` no mostró datos ni salidas aunque `salidas/`, `salidas_a/` y `salidas_b/` contenían archivos (`datos/` estaba vacía). Las reglas `datos/*`, `salidas/*`, `salidas_a/` y `salidas_b/` los ignoran. La carpeta `salidas_c/`, creada por la prueba 5.1, sí apareció como «Untracked» porque no tenía regla.

**Qué pasaría sin `.gitignore`.** Un `git add .` habría publicado datos y salidas en un repositorio público: más tamaño, un historial ensuciado con archivos binarios que cambian en cada corrida y, si algún día hubiera credenciales en esas carpetas, riesgo de exponerlas.

**Por qué no hace falta versionarlos.** Se regeneran con código + semilla + entorno, y las huellas prueban que quedan idénticos. Se versiona la receta, no el resultado.

Restauramos el repositorio con `rm -rf salidas_c`; `git status` quedó con `nothing to commit, working tree clean`.

---

## 6. Semilla del proyecto (M6)

**Idea preliminar:** brechas en el puntaje de Saber 11 entre colegios oficiales y privados. Es una idea provisional: se ajustará cuando el docente defina la base de datos del proyecto final (tercer corte).

**Archivos de `proyecto/`:**
- [proyecto/README.md](proyecto/README.md): pregunta, fuente, variables, herramientas y cómo reproducir (R1).
- `proyecto/requirements.txt`: numpy 2.1.3, pandas 2.2.3 y pyarrow 17.0.0, todas con `==` (R1, confirmación `e3d1d25`).
- `proyecto/Dockerfile`: imagen `python:3.12-slim-bookworm` que instala esas dependencias (R3, confirmación `7ecd60f`, «Proyecto: Dockerfile inicial»).

**Comprobación.** R1 y R3 ejecutaron `docker build --tag proyecto:0.1 proyecto/` sin errores y `docker run --rm proyecto:0.1` imprimió en ambos codespaces:

```
Entorno listo, pandas 2.2.3
```

**Por qué se construye con `proyecto/` al final del comando.** Esa carpeta es el contexto de construcción: ahí deben estar el `Dockerfile` y los archivos que `COPY` usa. Por eso `requirements.txt` va dentro de `proyecto/`.

---

## 7. Conceptos clave

| Concepto | En una frase | Dónde lo vimos |
|---|---|---|
| Reproducibilidad | Mismo resultado con código + datos + semilla + entorno | Todo el informe |
| Semilla | Estado inicial de un generador determinista | 5.1 |
| Huella SHA-256 | Identifica el contenido; un byte distinto la cambia por completo | 2, 4, 5.1 |
| Versión fijada | `==` hace determinista el entorno; `>=` no | 5.2 |
| `.gitignore` | Se versiona la receta, no el resultado | 5.3 |
| Etiqueta (tag) | Marcador fijo de una versión; no avanza como la rama | 4.3 |
| Fusión y fast-forward | Git une el trabajo de dos autores si tocan archivos distintos; si solo uno avanzó, simplemente adelanta la rama | 3, 4.1 |
| Regla de oro | `git pull --no-rebase --no-edit` antes de cada `git push` | 3 |
| `ENTRYPOINT` vs `CMD` | Ejecutable fijo que recibe argumentos, o comando por defecto reemplazable | 5.1, 5.2 |
| Capas y caché de Docker | Cada instrucción es una capa; se reutiliza si no cambió | 4.2 |
| Manifiesto | Registro de huellas y versiones de una corrida; sirve de evidencia | 5.2 |
| Usuario sin privilegios | La imagen corre como `analista` y no como root (privilegio mínimo) | Dockerfile |

---

## 8. Aprendizajes

- La reproducibilidad no es un solo ingrediente: si falla la semilla, el código, la versión de una biblioteca o los datos, falla el resultado. Cada prueba del Taller 2 rompió una de esas piezas.
- Una huella igual vale más que «se ve parecido»: dos tablas pueden parecer iguales y no serlo, y SHA-256 lo detecta.
- Que hoy coincidan las huellas con una versión sin fijar no prueba nada hacia el futuro: la prueba 5.2 dio `3 de 3` y aun así el entorno dejó de estar determinado.
- El manifiesto no registraba la versión de numpy. Una mejora para el proyecto es registrar en él todas las bibliotecas relevantes, para que una diferencia futura deje evidencia de su causa.
- Leer el Dockerfile y el script fue lo que resolvió el error del `ENTRYPOINT`, que nos pasó a los dos; copiar el comando del enunciado no bastaba.
- Para el proyecto final: fijar versiones desde el principio, dejar datos y salidas fuera del repositorio, y publicar con mensajes de confirmación específicos.