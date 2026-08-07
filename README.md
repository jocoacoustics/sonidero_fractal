# Sonidero Fractal v25

### Pedro K. Galindo Vera

**Sonidero Fractal** es una experiencia web autocontenida para explorar fractales del plano complejo y convertir una región de su dinámica en una onda sonora. La reconstrucción acústica parte de una magnitud derivada de la geometría visual y de una fase inicial obtenida de la propia órbita compleja del fractal; después utiliza **Griffin–Lim** para aproximar una STFT realizable.

La v25 es una versión de **estabilización y control**. Mantiene el motor matemático con fase fractal + Griffin–Lim, restaura el **rango de energía directamente en el Paso 3**, conserva el cajón de selección en escritorio y adopta en móvil un lenguaje gestual tipo mapa: un dedo desplaza, pellizcar acerca/aleja y doble toque amplía. También corrige la cobertura STFT/ISTFT del final del audio, añade controles explícitos contra WAV silencioso y cambia la reproducción interactiva a **Web Audio** para sincronizar el cursor con un reloj de audio continuo.

La aplicación sigue distribuida como **un único archivo HTML**.

---

## Inicio rápido

1. Abre `sonidero_fractal_pedro_k_galindo_vera_v25.html` en un navegador moderno.
2. Toca o haz clic en un fractal.
3. Explora, acerca y selecciona una región.
4. En **Paso 3 · Dale una voz** configura:
   - frecuencia mínima y máxima;
   - escala **Lineal / Logarítmica / Mel**;
   - duración;
   - máximo de iteraciones Griffin–Lim;
   - rango de energía **Fondo → Picos**.
5. Pulsa **Generar sonido**.
6. Durante la espera observa las ecuaciones y la convergencia espectral.
7. Reproduce el WAV. En escritorio, arrastra un **recuadro** sobre el espectrograma; en móvil, usa **un dedo para mover, pellizcar para zoom y doble toque para acercar**. Cada nueva ventana visible puede recalcularse con más detalle desde el mismo audio.
8. Abre ⚙ **Laboratorio** para volver a editar duración, banda, escala, Griffin–Lim, energía, detalle acústico y visualización.

---

# Flujo matemático

```text
Dinámica fractal compleja
        ↓
Región seleccionada
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
Espectrograma mostrado
```

El espectrograma final **no es una copia del fractal**. Se calcula nuevamente desde el WAV generado y representa la distribución tiempo–frecuencia que realmente consiguió la onda.

---

# Cambios de v25

## 1. Las escalas Lineal, Logarítmica y Mel vuelven al Paso 3

Las tres opciones ya no son solamente una forma diferente de mover el selector: también modifican **cómo las filas del fractal se asignan a frecuencias durante la síntesis**.

### Lineal

La posición vertical se distribuye uniformemente en Hz:

```text
p(f) = (f - fmin) / (fmax - fmin)
```

### Logarítmica

La geometría se distribuye uniformemente en log-frecuencia:

```text
p(f) = [ln(f) - ln(fmin)] / [ln(fmax) - ln(fmin)]
```

Para bandas que incluyen 0 Hz, la interfaz utiliza una transformación logarítmica desplazada para que el control siga siendo finito.

### Mel

Se emplea la escala perceptual:

```text
mel(f) = 2595 log10(1 + f/700)
```

seguida de una interpolación uniforme en el espacio Mel.

Por tanto, una misma región fractal puede producir tres sonificaciones distintas sin cambiar su geometría fuente.

La opción predeterminada en v25 es **Logarítmica**.

---

## 2. Paso 3 vuelve a tener controles útiles

El Paso 3 mantiene fuera de la vista principal parámetros puramente técnicos, pero conserva todas las decisiones perceptivas que cambian la experiencia:

- banda mínima y máxima;
- presets Voz, Aves, Insectos, Murciélagos y Banda completa;
- Lineal / Logarítmica / Mel;
- duración de 1 a 30 s mediante barra;
- máximo de iteraciones Griffin–Lim de 4 a 64 mediante barra;
- **rango de energía Fondo → Picos** mediante control doble en dB.

El número indicado es un **máximo**. Griffin–Lim conserva el criterio de parada temprana: si la mejora relativa del error espectral se estabiliza durante varias iteraciones, el motor puede terminar antes.

---

## 3. El Laboratorio recupera control acústico

### Detalle acústico

Un control de cinco niveles permite escoger:

```text
Automático · Rápida · Balanceada · Alta · Máxima
```

`Automático` deja que el motor determine el tamaño FFT según duración, banda y tamaño del fractal.

Los niveles manuales aumentan progresivamente la densidad frecuencial de la representación STFT. El `hop` continúa en aproximadamente:

```text
H = NFFT / 2
```

para mantener un solapamiento cercano al **50 %**.

La **frecuencia de muestreo no se deja libremente por debajo de lo permitido**: sigue seleccionándose de forma automática para respetar Nyquist según la frecuencia máxima solicitada.

### Rango de energía objetivo

Vuelve el control doble **Fondo → Picos** en dB. Esos valores definen el rango utilizado al transformar la intensidad fractal en magnitud espectral antes de Griffin–Lim.

Los valores por defecto continúan siendo aproximadamente:

```text
Fondo = -72 dB
Picos = -6 dB
```

---

## 4. Navegación del espectrograma: escritorio y móvil usan el gesto adecuado

Las antiguas barras azules de navegación siguen eliminadas.

### Escritorio

El comportamiento conserva la selección precisa con mouse:

```text
arrastrar un recuadro
        ↓
seleccionar tiempo × frecuencia
        ↓
ampliar
        ↓
recalcular STFT de la ventana visible
```

Además:

- rueda: zoom temporal;
- Shift + rueda: zoom frecuencial;
- Espacio + arrastrar: mover;
- T− / T+;
- F− / F+;
- Ver todo.

### Móvil

La pantalla táctil ya no intenta fingir que un dedo es un mouse. Usa el patrón de interacción de un mapa:

```text
1 dedo      → mover
pellizcar   → acercar / alejar en tiempo y frecuencia
doble toque → acercar alrededor de ese punto
```

Al terminar el gesto, Sonidero conserva la ventana visible y solicita una STFT de mayor detalle del **mismo WAV**. El gesto es inmediato desde la matriz en caché; el refinamiento real ocurre después.

El explorador fractal usa el mismo lenguaje gestual en móvil. En escritorio conserva el cajón de selección.

---

## 5. Reconstrucción Griffin–Lim

La condición inicial es:

```text
XF(t,f) = MF(t,f) exp(i φF(t,f))
```

con:

```text
φF(t,f) = arg(zórbita)
```

Griffin–Lim itera:

```text
X(j)
  → ISTFT
  → x(j)
  → STFT
  → fase realizable
  → reimponer MF
  → X(j+1)
```

esquemáticamente:

```text
X(j+1) = MF · exp[i · arg(STFT(ISTFT(X(j))))]
```

Durante la espera se muestran la iteración, el error, la mejora y miniaturas de:

```text
magnitud objetivo → espectro realizable actual
```

El error usado es una convergencia espectral relativa:

```text
E = || MF - |STFT(x)| ||F / || MF ||F
```

`E = 0` correspondería a coincidencia perfecta de magnitudes.

---

## 6. Audio: cobertura completa, taper corto y control contra silencio

La v25 corrige la cobertura temporal de la reconstrucción. El número de frames ya no se calcula truncando con `floor`; se usa cobertura por exceso para garantizar que el último frame alcance el final solicitado:

```text
frames = ceil((Naudio - NFFT) / hop) + 1
```

La ISTFT hace overlap-add y después recorta exactamente a la duración pedida.

Antes de exportar se aplica:

1. eliminación de DC;
2. taper de coseno de aproximadamente **8 ms** en los extremos;
3. primera y última muestra exactamente a cero;
4. normalización lineal de RMS y pico.

Además, el motor calcula RMS, pico, muestras no finitas y cantidad de muestras no nulas. Si detecta un resultado silencioso o inválido, **no declara la generación como terminada ni ofrece un WAV falso**.

---

## 7. Reproducción Web Audio y cursor continuo

La reproducción interactiva ya no usa el elemento `<audio>` como reloj principal. Sonidero reproduce directamente el `Float32Array` sintetizado mediante:

```text
Float32Array
   ↓
AudioBuffer
   ↓
AudioBufferSourceNode
   ↓
GainNode
   ↓
salida de audio
```

El cursor se calcula desde `AudioContext.currentTime`, por lo que el movimiento visual y el audio comparten el mismo reloj. Pausa, búsqueda y reanudación mantienen un `offset` explícito.

El archivo WAV sigue generándose por separado como PCM de 16 bits para descarga. Si una frecuencia de muestreo ultrasónica excede lo que el dispositivo puede reproducir, la preescucha puede remuestrearse al límite aceptado por Web Audio; el WAV exportado conserva su frecuencia de muestreo original.

---

## 8. Laboratorio: volver a editar la escucha

La rueda ⚙ permite modificar después de la primera generación:

- duración;
- frecuencia mínima y máxima;
- escala Lineal / Log / Mel;
- máximo de iteraciones Griffin–Lim;
- rango de energía Fondo → Picos;
- detalle acústico;
- controles de visualización del espectrograma.

Los cambios acústicos se aplican mediante **Regenerar audio**. Los controles puramente visuales solo recolorean la matriz espectral en caché.

---

# FFT, STFT, espectrograma e ISTFT

La **FFT** transforma un bloque temporal en coeficientes frecuenciales.

La **STFT** aplica FFT sucesivas a ventanas desplazadas de una señal:

```text
x[n] → ventanas → FFT → X(m,k)
```

`X(m,k)` es compleja:

```text
X(m,k) = |X(m,k)| exp(i φ(m,k))
```

El **espectrograma** es una representación real obtenida normalmente de la magnitud o potencia de esa STFT:

```text
S(m,k) = |X(m,k)|²
```

o en dB:

```text
SdB(m,k) = 20 log10 |X(m,k)|
```

La **ISTFT** aplica IFFT a cada frame y recompone la señal mediante ventana y overlap-add.

Si se conserva la STFT compleja completa, el recorrido:

```text
x → STFT → ISTFT → x̂
```

es prácticamente reversible hasta precisión numérica.

El problema de Sonidero es distinto: el fractal propone un campo tiempo–frecuencia que no necesariamente pertenece al conjunto de STFT consistentes. Griffin–Lim aproxima una señal temporal cuya magnitud espectral se acerque al objetivo.

---

# Fractales incluidos

- Mandelbrot
- Julia
- Burning Ship
- Tricorn / Mandelbar
- Multibrot con potencia `n` ajustable de 2 a 8
- Newton para `z³ − 1`

El minimapa del Paso 2 representa siempre la vista completa del fractal seleccionado y el rectángulo indica la región actual.

---

# Arquitectura

Todo permanece dentro del HTML:

- HTML y CSS responsivos;
- JavaScript vanilla;
- Canvas 2D;
- Web Workers creados desde `Blob`;
- worker de render fractal;
- worker DSP / Griffin–Lim;
- worker independiente para refinamiento espectral;
- caché de fractales e historial de vistas;
- caché de ventanas espectrales refinadas;
- WAV PCM mono de 16 bits generado localmente.

No se envían imágenes ni audios a un servidor.

---

# Publicación rápida en GitHub Pages

1. Crea un repositorio.
2. Copia el HTML al repositorio.
3. Puedes renombrarlo a `index.html`.
4. Añade este `README.md`.
5. En GitHub abre **Settings → Pages**.
6. Publica desde la rama principal.

---

# Validación realizada para v25

Se comprobó:

- sintaxis del JavaScript principal con Node;
- sintaxis independiente de ambos Web Workers;
- IDs HTML sin duplicados;
- todas las referencias internas `$('<id>')` apuntan a elementos existentes;
- ejecución reducida del worker DSP con escala logarítmica, Griffin–Lim y reconstrucción completa;
- en esa prueba el WAV terminó con primera y última muestra exactamente en `0` después del acondicionamiento de fronteras.

El Chromium headless disponible en el entorno no llegó a completar una prueba visual automatizada por limitaciones del proceso gráfico/DBus. Por ello los gestos táctiles deben validarse finalmente en un teléfono real.

---

## Autor

**Pedro K. Galindo Vera**

Firma de la experiencia:

```text
⟨𝐏, ε, ∂, r, Ω⟩ ⊗ ⟨Γ, α, ℒ, ι, ℕ, ∂, ∘⟩ ⊗ ⟨𝒱, ε, ℝ, 𝒜⟩
```
