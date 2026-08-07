# Sonidero Fractal v27

https://jocoacoustics.github.io/sonidero_fractal/

## Cambios principales de v27

- Exploración fractal con **overscan local 2×**: durante pan, pinch y rotación se mantiene una capa coarse de la misma región y orientación, evitando fondos negros y saltos visuales mientras llega el refinamiento.
- Se corrigió el **sentido de la rotación táctil** para que siga el giro natural de los dedos.
- El fractal se dibuja en modo *cover* preservando proporciones, para aprovechar toda el área disponible sin franjas negras laterales.
- En el Paso 3, la barra de frecuencias es **siempre logarítmica**; Lineal/Log/Mel ya no aparecen como modos del selector. La escala visual del espectrograma sigue siendo **Lineal por defecto** y puede cambiarse a Log o Mel desde el Laboratorio.
- Los presets **Voz, Aves, Insectos, Murciélagos y Banda completa** configuran ahora un conjunto completo: banda, rango de energía, duración y máximo de iteraciones Griffin–Lim.
- Rediseño de sliders: la pista completa queda más discreta y el intervalo activo se marca con una banda más gruesa, sin contorno blanco entre los handles.
- Griffin–Lim conserva 64 iteraciones máximas por defecto y permite 4, 8, 16, 32, 64, 128 o 256, con parada anticipada por convergencia.
- La pantalla de transformación elimina texto redundante y agranda la comparación **Objetivo → sonido realizable**.
- Se elimina el mensaje flotante sobre el espectrograma; las instrucciones y estados pasan a la barra inferior, dejando el gráfico limpio.

### Pedro K. Galindo Vera

**Sonidero Fractal** es una experiencia web autocontenida para explorar fractales del plano complejo y convertir una región —incluida su orientación— en sonido. La aplicación construye una magnitud espectral a partir de la geometría visual del fractal, toma una fase inicial de su propia dinámica compleja y utiliza **Griffin–Lim** para buscar una STFT realizable cercana. El WAV resultante se vuelve a analizar para mostrar el **espectrograma del sonido realmente generado**.

La v27 reorganiza la experiencia alrededor de una idea simple: **el fractal es un mundo navegable, no una miniatura dentro de un panel**. El Paso 2 es mucho más inmersivo, la navegación móvil adopta pan + pinch + rotación tipo mapa, desaparece el minimapa permanente y una capa fractal de baja resolución evita fondos negros mientras se recalcula la vista fina.

La aplicación sigue distribuida como **un único archivo HTML** y no requiere servidor.

---

## Inicio rápido

1. Abre `sonidero_fractal_pedro_k_galindo_vera_v27.html` en un navegador moderno.
2. Toca o haz clic en Mandelbrot, Julia, Burning Ship, Tricorn, Multibrot o Newton.
3. Explora el fractal.
   - **PC:** arrastra un recuadro para acercarte; Espacio + arrastrar mueve; rueda hace zoom; `Alt + rueda` rota.
   - **Móvil:** un dedo mueve, dos dedos pellizcan para zoom y giran para rotar; doble toque acerca.
4. Pulsa **Usar esta región →**.
5. En el Paso 3 configura presets, rango de frecuencias, energía, duración y refinamiento Griffin–Lim.
6. Pulsa **Generar sonido →**.
7. Durante la reconstrucción observa la matemática, la iteración, el error espectral y la convergencia **Objetivo → realizable actual**.
8. Reproduce o descarga el WAV.
9. En el espectrograma final:
   - PC: arrastra una caja para ampliar una ventana tiempo × frecuencia.
   - móvil: mueve y pellizca como un mapa.
10. ⚙ **Laboratorio** permite volver a editar la escucha y cambiar la escala **visual** del espectrograma.

---

# Flujo matemático

```text
Dinámica fractal compleja
        ↓
Región + orientación elegidas
        ↓
Magnitud objetivo MF(t,f)
        +
Fase inicial φF(t,f) = arg(zórbita)
        ↓
XF(t,f) = MF(t,f) exp(i φF(t,f))
        ↓
Griffin–Lim
        ↓
STFT realizable X*(t,f)
        ↓
ISTFT
        ↓
WAV x[n]
        ↓
STFT del WAV
        ↓
Espectrograma real mostrado
```

La aplicación distingue deliberadamente tres objetos:

- **Fractal fuente:** geometría y dinámica del plano complejo.
- **Campo tiempo–frecuencia objetivo:** la magnitud visual más una fase inicial compleja.
- **Espectrograma final:** representación calculada nuevamente desde el WAV que realmente se escucha.

Por tanto, el espectrograma final no es una copia decorativa del fractal.

---

# Novedades de v27

## 1. Paso 2 inmersivo

Se elimina el panel lateral y el minimapa permanente. El canvas fractal ocupa la mayor superficie disponible y todos los controles quedan **debajo** del mundo fractal.

El mapa no contiene botones flotantes. La única superposición posible es el recuadro temporal de selección en escritorio mientras el usuario lo está dibujando.

Los controles inferiores incluyen:

- lugar destacado;
- potencia `n` para Multibrot;
- historial Anterior / Siguiente;
- Acercar / Alejar en escritorio;
- Vista completa;
- Reorientar cuando la cámara está girada;
- detalle fractal bajo un bloque desplegable;
- Atrás / Usar esta región.

En móvil los botones de zoom se ocultan porque el gesto pinch es la interacción principal.

---

## 2. Cámara del plano complejo con rotación real

La cámara ya no se describe solo por centro y escala:

```text
(cx, cy, escala, θ)
```

Para cada píxel se aplica una transformación rotada antes de evaluar el fractal. Esquemáticamente:

```text
[x]   [cx]       [ cosθ  -sinθ ] [u]
[y] = [cy] + s · [ sinθ   cosθ ] [v]
```

La rotación no es un giro cosmético de una imagen ya calculada: el Worker vuelve a evaluar el plano complejo desde esa orientación.

Esto importa acústicamente porque Sonidero conserva la convención:

```text
horizontal → tiempo
vertical   → frecuencia
```

Rotar una estructura fractal cambia cómo esa estructura se distribuye en tiempo y frecuencia y, por tanto, puede cambiar el sonido.

**Vista completa** restaura centro, escala y `θ = 0`. Cuando `θ ≠ 0`, aparece debajo del mapa el botón **Reorientar** con el ángulo actual.

---

## 3. Navegación móvil tipo mapa

En el fractal:

```text
1 dedo       → desplazar
pinch        → acercar / alejar
giro 2 dedos → rotar
doble toque  → acercar alrededor del punto
```

El centro del gesto se conserva como ancla en el plano complejo, de manera que el punto bajo los dedos permanece estable mientras cambia escala y orientación.

En escritorio se conserva la selección precisa mediante recuadro y se añade:

```text
Alt + rueda → rotar
```

El espectrograma móvil usa pan + pinch + doble toque, pero **no permite rotación**, porque sus ejes tienen significado físico:

```text
x = tiempo
y = frecuencia
```

---

## 4. Fondo fractal multirresolución: nunca negro al navegar

La v27 reemplaza la función visual del minimapa por una capa interna de contexto.

El explorador mantiene simultáneamente:

```text
capa coarse, amplia y de baja resolución
                +
vista actual de alta resolución
```

Mientras el usuario mueve, pellizca o rota, la vista fina se transforma inmediatamente y la capa amplia queda detrás para cubrir zonas todavía no refinadas. Al terminar el gesto, el Worker calcula la nueva cámara y sustituye progresivamente la aproximación por el render correcto.

La idea perceptiva es:

```text
coarse disponible
      ↓
preview de la nueva cámara
      ↓
refinamiento de alta resolución
```

Así las regiones recién expuestas no deben aparecer como un fondo negro mientras llega el cálculo nuevo.

---

# Paso 3 · Dale una voz

La jerarquía se reorganizó para que los controles sigan el orden mental del usuario.

## 1. Presets y rango de frecuencias

Los presets aparecen primero:

```text
Voz · Aves · Insectos · Murciélagos · Banda completa
```

Después se muestra la barra doble y, debajo, `fmin` y `fmax` en una sola fila.

### Lineal / Logarítmica / Mel: solo controlan la barra

Esta distinción es importante en v27.

Los botones:

```text
Lineal · Logarítmica · Mel
```

**no cambian la sonificación**. Solamente modifican la relación entre la posición del deslizador y los Hz para hacer más cómoda la selección de una banda muy amplia.

Por ejemplo, la forma logarítmica permite manipular con mayor precisión frecuencias graves sin dedicar casi toda la barra a las frecuencias altas.

Una vez seleccionada la banda, el motor recibe solamente:

```text
fmin
fmax
```

La asignación vertical fractal → frecuencia usada por el motor acústico es **lineal**.

---

## 2. Rango de energía

El control doble **Fondo ↔ Picos** permanece visible en el Paso 3 porque sí modifica la magnitud objetivo:

```text
D(t,f) ∈ [Dfondo, Dpicos]
M(t,f) = 10^(D(t,f)/20)
```

El rango disponible se amplía hasta aproximadamente `-140 dB` para permitir experimentación, manteniendo como valores iniciales:

```text
Fondo = -72 dB
Picos = -6 dB
```

---

## 3. Duración

La duración principal puede ajustarse entre:

```text
1 s … 60 s
```

La duración inicial es `10 s`.

---

## 4. Refinamiento Griffin–Lim

La barra deja de ser lineal y usa potencias de dos:

```text
4 · 8 · 16 · 32 · 64 · 128 · 256
```

El valor predeterminado es:

```text
64 iteraciones máximas
```

El valor seleccionado es un **máximo**, no una obligación. El motor mantiene parada temprana cuando la mejora relativa del error se estabiliza.

Esto permite desde pruebas rápidas hasta reconstrucciones experimentales mucho más intensivas sin llenar la interfaz de números arbitrarios.

---

# Griffin–Lim y convergencia visible

La condición inicial utiliza la fase fractal:

```text
φF(t,f) = arg(zórbita)
XF = MF exp(i φF)
```

Después Griffin–Lim alterna:

```text
X(j)
  ↓ ISTFT
x(j)
  ↓ STFT
fase de una STFT realizable
  ↓
reimponer MF
  ↓
X(j+1)
```

Durante el cálculo la aplicación muestra:

- iteración actual / máximo;
- error espectral;
- mejor error encontrado;
- mejora respecto de la primera iteración;
- miniatura de la magnitud objetivo;
- miniatura del espectro realizable actual;
- ecuación correspondiente a la etapa.

La espera forma parte de la experiencia matemática en vez de ser una barra de carga opaca.

---

# Espectrograma final: Lineal por defecto

El espectrograma final siempre comienza visualmente en **escala Lineal**, independientemente de si el usuario usó Lineal, Log o Mel para mover la barra de frecuencias del Paso 3.

Desde ⚙ **Laboratorio** se puede cambiar la escala visual a:

```text
Lineal · Logarítmica · Mel
```

Ese cambio es únicamente una transformación de la visualización del **mismo espectrograma**. No genera otro WAV.

La v27 actualiza conjuntamente:

1. el muestreo vertical del raster del espectrograma;
2. la posición de las frecuencias;
3. las etiquetas y ticks del eje Y;
4. el comportamiento del zoom y pan frecuencial.

Por tanto, Log o Mel ya no deforman la imagen dejando un eje lineal incorrecto.

---

# Laboratorio

⚙ permite recuperar control después de la generación sin recargar la interfaz principal.

Incluye:

- duración;
- Griffin–Lim hasta 256;
- frecuencia mínima y máxima;
- rango de energía;
- detalle acústico;
- escala **visual** del espectrograma;
- diagnósticos de sample rate, FFT, hop, solapamiento, error y resolución tiempo–frecuencia.

El motor mantiene sample rate automático para proteger Nyquist.

La configuración acústica básica usa aproximadamente:

```text
H = NFFT / 2
```

es decir, 50 % de solapamiento.

---

# Integridad del audio

Antes de aceptar una generación, el Worker comprueba:

```text
muestras finitas
RMS
pico
número de muestras no nulas
```

Un resultado silencioso o con `NaN/∞` se considera un error y no llega a la pantalla final como si fuera un WAV válido.

La reconstrucción utiliza:

- cobertura temporal completa mediante `ceil` en el número de frames;
- Hann;
- overlap-add normalizado;
- eliminación de DC;
- taper corto en los extremos;
- normalización lineal de RMS/pico.

No se aplica `tanh` final, para evitar una distorsión espectral innecesaria después de Griffin–Lim.

La reproducción interactiva usa **Web Audio / AudioBuffer** y el cursor sigue `AudioContext.currentTime`. El WAV descargado continúa siendo PCM de 16 bits.

---

# Zoom espectral real

Al ampliar el espectrograma no se vuelve a ejecutar Griffin–Lim ni se crea un nuevo sonido.

El audio ya existe:

```text
WAV x[n]
   ↓
seleccionar ventana visible
   ↓
STFT adaptativa de esa ventana
   ↓
espectrograma refinado
```

La matriz base sigue visible mientras se calcula el detalle nuevo.

---

# Fractales incluidos

- Mandelbrot
- Julia
- Burning Ship
- Tricorn / Mandelbar
- Multibrot, con potencia `n = 2 … 8`
- Newton para `z³ − 1`

Cada uno conserva regiones destacadas para comenzar a explorar.

---

# Arquitectura

Todo vive en un único HTML:

```text
UI principal
├── Wizard de cuatro pasos
├── Canvas fractal
├── Canvas coarse de contexto
├── Canvas espectrograma
├── Web Audio
└── Laboratorio

Workers Blob
├── Worker fractal
│   ├── render progresivo
│   ├── cámara rotada
│   └── capa coarse
│
└── Worker DSP
    ├── magnitud objetivo
    ├── fase fractal
    ├── Griffin–Lim
    ├── ISTFT
    ├── validación de audio
    ├── STFT final
    └── STFT refinada por viewport
```

No se envían datos a un servidor.

---

# Publicación en GitHub Pages

La opción mínima es renombrar el HTML a `index.html` y subirlo al repositorio junto con este README.

Estructura:

```text
/
├── index.html
└── README.md
```

En GitHub:

```text
Settings
→ Pages
→ Deploy from a branch
→ main / root
```

La aplicación no necesita build, Node, Python ni backend para ejecutarse.

---

# Validación de v27

Para esta entrega se verificó:

- sintaxis del JavaScript principal mediante Node;
- sintaxis independiente de los dos Workers;
- IDs HTML únicos;
- ausencia de referencias `$('<id>')` hacia elementos inexistentes;
- ejecución aislada del Worker fractal con una cámara rotada;
- ejecución aislada del Worker DSP con `viewRotation`, STFT/ISTFT y Griffin–Lim;
- en la prueba DSP se produjo una señal no silenciosa con RMS ≈ `0.1995`, pico ≈ `0.4398` y error espectral ≈ `0.2038` tras 4 iteraciones de prueba.

Los gestos multitáctiles deben validarse además en hardware móvil real, porque su sensación depende del navegador, densidad de pantalla y frecuencia de eventos táctiles.

---

# Próxima gran etapa

La próxima expansión conceptual prevista es una sección autocontenida de divulgación matemática que explique desde cero:

```text
onda
→ números complejos
→ Fourier
→ FFT
→ ventanas
→ STFT
→ magnitud y fase
→ espectrograma
→ ISTFT
→ consistencia
→ problemas inversos
→ Griffin–Lim
→ dinámica fractal
→ Sonidero Fractal
```

La intención es que una persona pueda llegar sin conocer análisis de Fourier y terminar entendiendo por qué un fractal puede convertirse en sonido y por qué el espectrograma final no tiene que ser idéntico a la imagen fuente.
