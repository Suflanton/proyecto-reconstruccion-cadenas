# Reconstrucción de cadenas de ADN

Proyecto en **Scala 3** que resuelve el Problema de Reconstrucción de Cadenas (PRC): recuperar una cadena desconocida `S` de longitud `N` sobre el alfabeto `{a, c, g, t}` usando solo un oráculo `Ψ_S` que indica si una secuencia es subcadena de `S`.

El enunciado del problema está en [`El_problema_de_reconstrucción_de_cadenas.pdf`](El_problema_de_reconstrucción_de_cadenas.pdf).

## Qué incluye

- Soluciones **secuenciales**: ingenua, mejorada, turbo, turbo mejorada y turbo acelerada (con árboles de sufijos).
- Versiones **paralelas** de esas soluciones (paralelismo de datos y de tareas).
- Medición de tiempos con **ScalaMeter** y exportación de resultados a CSV.

## Requisitos

- JDK 8 o superior
- [sbt](https://www.scala-sbt.org/)

Scala `3.3.6`. Dependencias principales: `scalameter-core`, `scala-parallel-collections` y `munit` (tests).

## Compilar y ejecutar

```bash
sbt compile
sbt run
```

Si sbt pregunta qué `main` ejecutar, elige `TestRunner`.

`TestRunner` genera una cadena aleatoria de longitud `n = 2^k`, compara `reconstruirCadenaTurboMejorada` (secuencial) con `reconstruirCadenaTurboMejoradaPar` (paralela) y escribe un CSV `resultados_acelerada_YYYYMMDD_HHMMSS.csv` en la raíz del proyecto.

## Uso de los algoritmos

```scala
val sec = "gaatccagat".toList
val or = Oraculo.crearOraculo(1)(sec)

ReconstCadenas.reconstruirCadenaIngenuo(sec.length, or)
ReconstCadenas.reconstruirCadenaMejorado(sec.length, or)
ReconstCadenas.reconstruirCadenaTurbo(sec.length, or)
ReconstCadenas.reconstruirCadenaTurboMejorada(sec.length, or)
ReconstCadenas.reconstruirCadenaTurboAcelerada(sec.length, or)

val umbral = 1000
ReconstCadenasPar.reconstruirCadenaIngenuoPar(umbral)(sec.length, or)
ReconstCadenasPar.reconstruirCadenaMejoradoPar(umbral)(sec.length, or)
ReconstCadenasPar.reconstruirCadenaTurboPar(umbral)(sec.length, or)
ReconstCadenasPar.reconstruirCadenaTurboMejoradaPar(umbral)(sec.length, or)
ReconstCadenasPar.reconstruirCadenaTurboAceleradaPar(umbral)(sec.length, or)
```

El parámetro `umbral` decide a partir de qué tamaño de candidatas se activa el paralelismo. Por debajo de ese tamaño se usa la versión secuencial para evitar sobrecarga.

## Estructura

| Paquete | Rol |
| --- | --- |
| `Oraculo` | Alfabeto y oráculo (`crearOraculo`) |
| `ArbolSufijos` | Trie y árbol de sufijos para filtrar candidatas |
| `ReconstCadenas` | Algoritmos secuenciales |
| `ReconstCadenasPar` | Algoritmos paralelos |
| `common` | `task` / `parallel` sobre `ForkJoinPool` |
| `TestRunner` | Benchmarks con ScalaMeter |
