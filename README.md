# Sonidero Fractal v22

### Pedro K. Galindo Vera

**Sonidero Fractal** es una experiencia web interactiva para explorar fractales del plano complejo, seleccionar una región, convertir su geometría en sonido y analizar el espectrograma del WAV generado.

La aplicación está contenida en **un único archivo HTML** y adapta automáticamente su interfaz a escritorio y dispositivos móviles.

---

## Identidad

La experiencia se presenta con la firma matemática:

```text
⟨𝐏, ε, ∂, r, Ω⟩ ⊗ ⟨Γ, α, ℒ, ι, ℕ, ∂, ∘⟩ ⊗ ⟨𝒱, ε, ℝ, 𝒜⟩
```

correspondiente a **Pedro K. Galindo Vera**.

---

## Fractales disponibles

La versión 22 se concentra en fractales del plano complejo:

- **Mandelbrot**
- **Julia**
- **Burning Ship**
- **Tricorn / Mandelbar**
- **Multibrot**
- **Newton**

### Multibrot

Multibrot permite modificar la potencia `n` entre 2 y 8 mediante controles externos ▲ ▼.

La iteración general es:

```text
zₙ₊₁ = zₙⁿ + c
```

---

## Flujo de uso

### 1. Elegir un fractal

El inicio usa un carril horizontal centrado con efecto *peek*.

- clic o toque sobre una tarjeta = abrir ese fractal;
- no existe un botón adicional de “Explorar”;
- cada fractal dispone de una miniatura propia.

### 2. Explorar

El visor permite:

- selección rectangular para acercarse;
- zoom con rueda;
- paneo;
- acercar / alejar;
- volver a la vista completa;
- navegar por historial con **Anterior / Siguiente**;
- usar directamente la vista completa sin seleccionar una región;
- elegir presets de lugares destacados;
- controlar la resolución del render fractal.

El historial conserva las vistas ya calculadas para recuperarlas inmediatamente cuando existe caché.

### 3. Dar voz a la imagen

La imagen fractal se interpreta como una distribución tiempo–frecuencia para construir el sonido.

Controles principales:

- frecuencia mínima y máxima;
- escala **Lineal**, **Log** o **Mel**;
- presets de **Voz**, **Aves**, **Insectos**, **Murciélagos** y **Banda completa**;
- resolución acústica;
- rango de energía **Fondo / Picos**;
- carácter del sonido.

### 4. Escuchar y analizar

La vista final mantiene únicamente dos modos principales:

- **Fractal fuente**
- **Espectrograma WAV**

El espectrograma mostrado se calcula a partir del WAV realmente generado.

---

## Sonificación

El flujo conceptual es:

```text
Fractal fuente
    ↓
Magnitud tiempo–frecuencia
    ↓
Espectro complejo + fase
    ↓
ISTFT
    ↓
WAV
    ↓
STFT de verificación
    ↓
Espectrograma final
```

La imagen fractal es, por tanto, la **fuente de la sonificación**; el espectrograma final sirve para visualizar el contenido tiempo–frecuencia del audio producido.

---

## Matemática durante el cálculo

Los tiempos de espera forman parte de la experiencia. Durante el render y la generación acústica se muestran fórmulas relacionadas con la etapa activa.

Ejemplos:

### Mandelbrot

```text
zₙ₊₁ = zₙ² + c,    z₀ = 0
```

### Julia

```text
zₙ₊₁ = zₙ² + c
```

### Burning Ship

```text
zₙ₊₁ = (|Re(zₙ)| + i|Im(zₙ)|)² + c
```

### Tricorn

```text
zₙ₊₁ = conjugado(zₙ)² + c
```

### Newton

Para `f(z) = z³ − 1`:

```text
zₙ₊₁ = zₙ − (zₙ³ − 1) / (3zₙ²)
```

### Reconstrucción del sonido

```text
Xₘ,ₖ = Aₘ,ₖ e^(iφₘ,ₖ)
```

```text
x[n] = ISTFT{Xₘ,ₖ}
```

---

## Minimapa de contexto

El explorador incluye un minimapa que representa la vista completa del fractal actual y sitúa sobre ella la ventana explorada.

Esto permite mantener orientación espacial durante sucesivos acercamientos y cambios de región.

---

## Zoom del espectrograma

La vista espectral permite navegar independientemente en:

- **tiempo**: `T−` / `T+`;
- **frecuencia**: `F−` / `F+`;
- rueda para zoom temporal;
- `Shift + rueda` para zoom frecuencial;
- arrastre para paneo;
- **Ver todo** para restaurar la ventana completa.

El sistema reutiliza la información en caché para mantener la interacción fluida y puede refinar la representación visible cuando corresponde.

---

## Resolución

### Fractal

La resolución del fractal puede aumentarse desde niveles rápidos hasta niveles de mayor detalle. Los renders refinados se conservan en memoria mientras permanece abierta la aplicación.

### Audio y espectrograma

El selector de **Resolución espectral** actualiza automáticamente la generación, sin requerir un botón separado de “Aplicar”.

El progreso se muestra durante el recálculo.

---

## Diseño responsive

La misma aplicación adapta su UX según el dispositivo.

### Escritorio

- visor principal amplio;
- panel lateral de exploración;
- controles compactos;
- mouse, rueda y teclado;
- laboratorio técnico disponible como panel lateral.

### Móvil

- controles táctiles más grandes;
- panel de exploración adaptado;
- barras de frecuencia y energía a ancho útil;
- navegación táctil;
- panel técnico adaptado a pantallas estrechas.

No existe una versión móvil separada: **todo vive en el mismo HTML responsive**.

---

## Laboratorio avanzado

La interfaz principal usa términos simples, mientras el panel avanzado mantiene controles técnicos como:

- frecuencia de muestreo;
- FFT;
- salto temporal;
- niveles dBFS;
- gamma imagen → audio;
- modo de potencia;
- síntesis coherente o difusa;
- brillo, contraste y gamma visual;
- piso visual del espectrograma.

---

## Arquitectura técnica

La aplicación utiliza:

- HTML, CSS y JavaScript sin framework obligatorio;
- Canvas 2D;
- Web Workers para evitar bloquear la interfaz;
- cálculo fractal fuera del hilo principal;
- DSP e FFT/ISTFT en worker;
- caché de fractales, audio y espectrogramas;
- WAV generado localmente en el navegador.

No requiere servidor para ejecutar la experiencia básica.

---

## Inicio rápido

Descarga:

```text
sonidero_fractal_pedro_k_galindo_vera_v22.html
```

Y ábrelo directamente en un navegador moderno.

También puede servirse con un servidor local:

```bash
python -m http.server 8000
```

Luego abre:

```text
http://localhost:8000/sonidero_fractal_pedro_k_galindo_vera_v22.html
```

---

## GitHub Pages

La forma más simple de publicarlo es renombrar el HTML:

```bash
mv sonidero_fractal_pedro_k_galindo_vera_v22.html index.html
```

Después:

1. subir `index.html` y `README.md` al repositorio;
2. abrir **Settings → Pages**;
3. seleccionar la rama principal;
4. publicar desde la raíz del repositorio.

---

## Archivos recomendados del repositorio

```text
/
├── index.html
├── README.md
└── LICENSE        # opcional
```

---

## Compatibilidad

Recomendado para navegadores modernos con soporte para:

- Canvas;
- Web Workers;
- Web Audio / reproducción de WAV;
- Pointer Events;
- Blob URLs.

Para cálculos de máxima resolución se recomienda un navegador actualizado y un equipo con varios núcleos de CPU.

---

## Autor

**Pedro K. Galindo Vera**

Concepto, diseño de la experiencia, enfoque matemático y dirección del proyecto.

---

## Versión

**Sonidero Fractal v22**

Versión enfocada en consolidación de UX, sonificación de fractales del plano complejo, navegación espectral y presentación matemática de la experiencia.
