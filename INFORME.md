# Informe del taller · Unidad 6 · Grupo NN

## 1. Repositorio e integrantes

Repositorio: https://github.com/CristianVallejo0104/lab05-reproducibilidad

| Integrante | Usuario de GitHub | Rol |
|---|---|---|
| Cristian Vallejo | @CristianVallejo0104 | R1 (responsable del repositorio) y R2 (responsable de datos) |
| Juan Pablo Tibamoso | @Juanxct | R3 (responsable de análisis) |

Como el grupo es de 2 integrantes, R1 asume también el rol de R2.

## 2. Reproducción de la versión 1.0 (M2)

Los dos integrantes obtuvieron, en su propio codespace, la misma última línea de `./reproducir.sh`:

`Reproducción exacta: 3 de 3 salidas coinciden.`

Huellas SHA-256 obtenidas por **@CristianVallejo0104** y por **@Juanxct** (idénticas entre sí y con los valores de referencia):

```
154b62824a5a7f02e37a5e7a0a93e46ddd0237f2d88a57a8048800650b15a59a  resumen_ciudad.parquet
0c598115bb5676dd920eb4ada9a1be22dc6509f79aad616d795a65e337bfbb4b  resumen_ciudad.csv
568842db5a3964bdf2714a947311d127a9e35722593c2f12cba9b2ced58e74b2  tarifa_media_ciudad.png
```

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

Conteo de confirmaciones por autor (`git log --format="%an" | sort | uniq -c`):

```
3 Cristian Vallejo
5 Juanxct
```

Push rechazado: no hubo. Cristian publicó primero y Juan Pablo ejecutó `git pull --no-rebase --no-edit` antes de su `git push`, así que su push ya iba sobre la versión actual del remoto. Git unió los dos cambios por fusión automática (`Merge made by the 'ort' strategy`) porque se modificaron archivos distintos.