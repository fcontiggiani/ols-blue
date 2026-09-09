# MCO como BLUE — El teorema de Gauss–Markov en acción

Simulador interactivo, autocontenido y sin dependencias de compilación, para demostrar empíricamente por qué el estimador de Mínimos Cuadrados Ordinarios (MCO) es el *Best Linear Unbiased Estimator* (BLUE) bajo los cinco supuestos clásicos de Gauss–Markov (Wooldridge, 2020, cap. 3).


**[▶ Abrir el simulador](https://fcontiggiani.github.io/ols-blue/mco-como-blue.html)**

![Captura del simulador: ](screenshot.png)

---

## Descripción

La herramienta genera una **población finita** a partir de un proceso generador de datos (PGD) totalmente configurable,

```
Y = β₀ + β₁X₁ + β₂X₂ + β₃Z + u
```

y permite extraer **muestras repetidas** sobre esa población para observar, de manera empírica, las propiedades de insesgadez, eficiencia y consistencia de β̂. Cada uno de los cinco supuestos MLR.1–MLR.5 puede satisfacerse o violarse deliberadamente desde los controles, con un panel de diagnóstico que evalúa su cumplimiento en vivo.

A diferencia de una demostración puramente teórica, el simulador distingue explícitamente el **β estructural** del PGD (el valor verdadero, fijado por quien lo usa) del **β poblacional** (el coeficiente de MCO calculado sobre la población completa). Bajo violaciones de exogeneidad o colinealidad de variables correlacionadas omitidas, ambos valores divergen; el muestreo aleatorio simple es insesgado para el segundo, no necesariamente para el primero. Esta distinción evita una demostración estadísticamente engañosa y se muestra de forma simultánea en las tablas y en el panel de coordenadas.

## Características principales

| Panel / control | Descripción |
|---|---|
| **Parámetros estructurales del PGD** | β₀–β₃ configurables; casillas para decidir qué variables se *realizan* (afectan a Y) en la población — permite construir el caso complementario al sesgo por omisión: una variable genuinamente irrelevante incluida en la estimación. |
| **Regresores X₁, X₂** | Distribución normal o asimétrica; correlación nominal entre ambos (aproxima la colinealidad, MLR.3). |
| **Proceso del error *u*** | Media, dispersión y distribución configurables; correlación con X₁/X₂ (endogeneidad, rompe MLR.4); interruptor de heterocedasticidad Var(u\|X₁) creciente en \|X₁\| (rompe MLR.5). |
| **Población** | Generación única de N observaciones (por defecto 200.000) con semilla explícita (PRNG `mulberry32`) para garantizar reproducibilidad. |
| **Diseño muestral** | Tamaño de muestra n ∈ {10, 50, 100, 1000, 10000}; selección de regresores a incluir en el modelo estimado; número de repeticiones Monte Carlo R. |
| **Dispersión 3D** | Nube de puntos y plano de mejor ajuste, con Y en la ordenada y los dos regresores elegidos en las abscisas. |
| **Dispersión 2D** | Y vs. un regresor seleccionable; la variable restante se codifica por color o tamaño; recta MCO fijable entre remuestreos sucesivos para visualizar la variabilidad muestral. |
| **Histogramas de β̂** | Distribución muestral de cada coeficiente sobre las R repeticiones, con ajuste normal empírico (TLC) y referencia al valor poblacional. |
| **Coordenadas de los β̂** | Nube conjunta de dos coeficientes con elipse de confianza empírica (~95 %), ilustrando la propiedad de mínima varianza. |
| **Tema claro / oscuro** | Conmutador persistido por navegador; recolorea los seis paneles de Plotly en vivo. |

## Fundamento teórico

El panel de diagnóstico evalúa en vivo los cinco supuestos de Gauss–Markov (notación de Wooldridge):

1. **MLR.1 — Linealidad en los parámetros**: se cumple por construcción del simulador.
2. **MLR.2 — Muestreo aleatorio**: cada muestra es una extracción aleatoria simple sin reemplazo sobre la población.
3. **MLR.3 — No colinealidad perfecta**: controlado por la correlación nominal ρ(X₁,X₂).
4. **MLR.4 — Media condicional nula** E[u\|X]=0: controlado por μ(u) y por la correlación del error con los regresores (endogeneidad).
5. **MLR.5 — Homocedasticidad** Var(u\|X)=σ²: controlado por el interruptor de heterocedasticidad.

Se incluye además una nota separada sobre la **normalidad** de *u* como supuesto adicional (MLR.6, ajeno a Gauss–Markov): no es requisito para que MCO sea BLUE, pero sí para la validez exacta de la inferencia t/F en muestras finitas — distinción que puede contrastarse comparando los histogramas de β̂ para n pequeño frente a n grande.

## Estructura del repositorio

```
.
├── index.html      # Simulador completo (HTML + CSS + JavaScript)
├── README.md        # Este documento
└── LICENSE           # A definir (véase Licencia)
```

## Stack técnico

- **JavaScript vanilla** (sin frameworks ni transpilación): generación de la población, álgebra lineal para el ajuste MCO (eliminación de Gauss–Jordan, hasta 4×4), muestreo sin reemplazo mediante Fisher–Yates parcial y generador pseudoaleatorio con semilla (`mulberry32`).
- **[Plotly.js](https://plotly.com/javascript/) 3.5.1** (vía cdnjs) para los seis paneles gráficos (dispersión 3D, dispersión 2D, histogramas, coordenadas).
- **Google Fonts**: Source Serif 4 (títulos), IBM Plex Sans (interfaz), IBM Plex Mono (valores numéricos).
- Sin dependencias de servidor: toda la simulación se ejecuta en el navegador del usuario.

## Notas metodológicas y limitaciones conocidas

- Las variables no normales se generan mediante una transformación no lineal de normales estándar (posiblemente correlacionadas), reestandarizada empíricamente sobre la población completa para alcanzar exactamente la media y desviación estándar configuradas; la correlación nominal entre regresores es, por lo tanto, aproximada y se reporta también su valor **realizado** para transparencia.
- Los errores estándar mostrados en la tabla de la muestra vigente se calculan bajo el supuesto de homocedasticidad (MLR.5); cuando dicho supuesto se viola deliberadamente, la brecha entre la desviación estándar Monte Carlo y el error estándar teórico promedio hace evidente la invalidez de esa fórmula clásica.
- El tamaño poblacional (N) y el número de repeticiones Monte Carlo (R) están acotados para mantener la interfaz responsiva; valores extremos pueden requerir unos segundos de cómputo.

## Referencias

Stock, J. H., & Watson, M. W. (2019). *Introduction to Econometrics* (4.ª ed.). Pearson.

Wooldridge, J. M. (2020). *Introductory Econometrics: A Modern Approach* (7.ª ed.). Cengage.

## Autoría

Federico Contiggiani — Universidad Nacional de Río Negro (UNRN) · Instituto de Investigaciones en Políticas Públicas y Gobierno (IIPPyG) · CONICET.

## Licencia

_A definir por el autor._ Para materiales de cátedra de distribución abierta suele resultar apropiada una licencia [MIT](https://opensource.org/licenses/MIT) (código) o [Creative Commons BY 4.0](https://creativecommons.org/licenses/by/4.0/deed.es) (contenido pedagógico); cualquiera de las dos puede incorporarse agregando el archivo `LICENSE` correspondiente en la raíz del repositorio.
