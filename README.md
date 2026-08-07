# Sonidero Fractal

https://jocoacoustics.github.io/sonidero_fractal/

**Explora geometrías imposibles. Escucha sus patrones.**

Una experiencia web interactiva creada por **Pedro K. Galindo Vera** para explorar fractales de Mandelbrot y Julia, seleccionar regiones, convertir su estructura visual en audio y analizar el resultado mediante un espectrograma interactivo.

La aplicación funciona directamente en el navegador y está distribuida como un único archivo HTML.

---

## Demo rápida

1. Abre `index.html` en un navegador moderno.
2. Elige **Mandelbrot** o **Julia**.
3. Explora el fractal completo o selecciona una región.
4. Define la banda de frecuencias y el carácter del sonido.
5. Genera el audio.
6. Reproduce, descarga el WAV y examina su espectrograma.

---

## Características

### Exploración fractal

- Fractales de **Mandelbrot** y **Julia**.
- Miniaturas psicodélicas en la pantalla inicial.
- Vista previa rápida seguida de refinamiento progresivo.
- Selección rectangular directa para acercarse a una región.
- Zoom, paneo y restauración de vista completa.
- Presets de regiones destacadas para cada familia fractal.
- Historial **Anterior / Siguiente** con recuperación instantánea desde caché.
- Resolución fractal configurable desde vista previa hasta máxima definición.
- Minimapa de contexto tras una selección.

### Sonificación

- Conversión de la imagen fractal a una magnitud espectral.
- Banda de frecuencia mínima y máxima editable.
- Escalas de control:
  - lineal;
  - logarítmica;
  - Mel.
- Presets rápidos:
  - Voz;
  - Aves;
  - Insectos;
  - Murciélagos;
  - Banda completa.
- Perfiles sonoros:
  - Natural y definido;
  - Limpio y fino;
  - Textura envolvente;
  - Experimental.
- Resolución acústica rápida, balanceada, alta o máxima.
- Control simple del rango de energía: fondo y picos.
- Estimación del tiempo de generación.

### Espectrograma y reproducción

- Espectrograma calculado a partir del WAV generado.
- Eje temporal en segundos y eje frecuencial en Hz.
- Cursor sincronizado con la reproducción.
- Clic para saltar a una posición del audio.
- Zoom temporal con la rueda.
- Zoom frecuencial con `Shift + rueda`.
- Arrastre para mover la vista.
- Botón **Ver todo** para restaurar el encuadre.
- Controles visuales sin recalcular la STFT:
  - brillo;
  - contraste;
  - gamma;
  - piso visual en dB.
- Descarga del resultado en formato WAV.

### Interfaz adaptable

- Diseño de escritorio optimizado para mantener cada paso dentro de la pantalla.
- Navegación desde la vista final hacia los pasos de sonido o exploración.
- Panel técnico plegable.
- Interfaz móvil con:
  - pellizco para zoom;
  - arrastre para paneo;
  - modo explícito de selección;
  - panel inferior plegable;
  - laboratorio como bandeja inferior.

---

## Inicio rápido

### Opción 1: abrir directamente

Renombra el archivo principal:

```bash
mv sonidero_fractal_pedro_k_galindo_vera_v14.html index.html
```

Después abre `index.html` en Chrome, Edge, Firefox o Safari.

> La aplicación no requiere instalación, compilación ni backend.

### Opción 2: servidor local

Servir el archivo localmente evita restricciones particulares de algunos navegadores:

```bash
python -m http.server 8000
```

Luego visita:

```text
http://localhost:8000
```

---

## Publicación con GitHub Pages

1. Coloca el archivo principal como `index.html` en la raíz del repositorio.
2. Añade este archivo como `README.md`.
3. Sube ambos archivos a la rama `main`.
4. En GitHub abre **Settings → Pages**.
5. Selecciona **Deploy from a branch**.
6. Elige la rama `main` y la carpeta `/root`.
7. Guarda la configuración.

Estructura mínima recomendada:

```text
sonidero-fractal/
├── index.html
└── README.md
```

---

## Controles

### Explorador fractal en escritorio

| Acción | Control |
|---|---|
| Seleccionar región | Arrastrar una caja sobre el fractal |
| Mover la vista | `Espacio + arrastrar` |
| Zoom fino | Rueda del ratón |
| Acercar o alejar | Botones del panel lateral |
| Recuperar una vista | Anterior / Siguiente |
| Restaurar | Vista completa |

### Explorador fractal en móvil

| Acción | Control |
|---|---|
| Mover | Arrastrar con un dedo |
| Zoom | Pellizcar con dos dedos |
| Seleccionar región | Activar **Seleccionar región** y arrastrar |
| Abrir controles | Tocar la bandeja inferior |

### Espectrograma

| Acción | Control |
|---|---|
| Zoom temporal | Rueda |
| Zoom frecuencial | `Shift + rueda` |
| Mover la vista | Arrastrar |
| Saltar en el audio | Clic sobre el espectrograma |
| Restaurar vista | Doble clic o **Ver todo** |

---

## Laboratorio avanzado

El panel **Laboratorio** expone los parámetros técnicos sin recargar la experiencia principal:

- frecuencia mínima y máxima;
- duración;
- frecuencia de muestreo;
- tamaño FFT;
- salto temporal;
- nivel de fondo y nivel máximo;
- sensibilidad imagen→audio;
- modo de potencia visual;
- método de síntesis;
- brillo, contraste, gamma y piso visual del espectrograma.

Los controles visuales recolorean la matriz espectral almacenada en caché; no vuelven a calcular la STFT.

---

## Arquitectura técnica

Sonidero Fractal está construido con tecnologías web nativas:

- HTML5;
- CSS3;
- JavaScript sin frameworks;
- Canvas 2D;
- Web Audio mediante WAV generado en memoria;
- Web Workers embebidos mediante `Blob URL`.

### Motor fractal

- Cálculo en un Web Worker independiente.
- Ruta optimizada para la iteración cuadrática `z² + c`.
- Render por bloques con progreso incremental.
- Vista previa de baja resolución antes del refinamiento.
- Cancelación lógica mediante identificadores de trabajo.
- Caché de vistas para navegación instantánea en el historial.

### Motor acústico

- Ejecución en un segundo Web Worker.
- Mapeo de luminancia y bordes a magnitud espectral.
- Síntesis mediante fase coherente o textura difusa.
- FFT e ISTFT implementadas en JavaScript.
- Normalización y limitación del resultado.
- Cálculo único del espectrograma real del WAV.

### Cachés independientes

La aplicación conserva por separado:

- imagen fractal;
- audio generado;
- matriz espectral en dB;
- historial de vistas.

Esto evita recalcular al cambiar de pestaña, redimensionar la ventana o modificar únicamente la visualización.

---

## Privacidad y procesamiento

El cálculo del fractal, la generación de audio y el espectrograma se ejecutan localmente en el navegador.

La aplicación no envía el audio ni los parámetros fractales a un servidor. Las tipografías `Space Grotesk` e `Inter` se cargan desde Google Fonts; si no hay conexión, se utilizan fuentes de sistema como respaldo.

---

## Rendimiento

El tiempo de cálculo depende de:

- resolución del fractal;
- número de iteraciones;
- duración del audio;
- frecuencia de muestreo;
- tamaño FFT;
- salto temporal;
- potencia del dispositivo.

Para equipos modestos o celulares se recomienda comenzar con:

- fractal en resolución **Suave** o **Balanceada**;
- audio en resolución **Rápida** o **Balanceada**;
- duración de 5 segundos;
- frecuencia de muestreo de 48 kHz.

---

## Compatibilidad

Recomendado:

- Google Chrome o Chromium reciente;
- Microsoft Edge reciente;
- Mozilla Firefox reciente;
- Safari moderno.

La aplicación requiere soporte para:

- Web Workers;
- Canvas 2D;
- `Blob URL`;
- Typed Arrays;
- Pointer Events.

---

## Alcance

Sonidero Fractal es una experiencia experimental de visualización y sonificación matemática. La transformación imagen→audio es una decisión artística y computacional; no representa una medición acústica del fractal ni un modelo físico de sonido.

---

## Autor

**Pedro K. Galindo Vera**

Concepto, diseño, desarrollo y experimentación matemática-sonora.

---

## Versión

README preparado para **Sonidero Fractal v14**.
