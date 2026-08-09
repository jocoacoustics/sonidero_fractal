# Sonidero Fractal v35

https://jocoacoustics.github.io/sonidero_fractal/

Versión de estabilización sobre v33.

## Cambios v35

- Corrige la regresión que podía detener la generación con `Cannot set properties of null (setting disabled)` cuando el shell premium había retirado o reubicado controles heredados. El motor ya no depende de esos botones para completar la reconstrucción.
- Mantiene intacto el motor STFT/ISTFT/Griffin–Lim de la versión previa; la corrección es defensiva de interfaz, no un cambio de DSP.
- Reordena las escalas de los sliders: las marcas de referencia quedan **encima y pegadas a la barra**.
- La duración conserva la burbuja dinámica **debajo del thumb**, mostrando el valor exacto en segundos durante el arrastre.
- Griffin–Lim conserva sus saltos discretos; sus marcas también pasan arriba de la barra para homogeneidad.
- Aplica la misma lógica visual a sliders simples del panel avanzado cuando tienen escala visible.

**Autor:** Pedro K. Galindo Vera

## Cambios v33

- Duración: el valor seleccionado aparece justo debajo del thumb del slider.
- Acción sonora unificada por estado: **♪ Escuchar → ▶ Reproducir → ❚❚ Pausa**. Cualquier cambio que afecte la síntesis invalida el audio anterior y devuelve la acción a **Escuchar**.
- Eliminadas del bottom sheet final las acciones heredadas **Volver al fractal / Generar sonido**.
- Homogeneidad visual de sliders en todo el sheet expandido, incluidos gamma, brillo, contraste, piso visual y parámetros técnicos.
- Carrusel circular con asentamiento más conservador y menor interferencia del `scroll-snap` durante el gesto lento.
- Se mantiene la continuidad Fractal ↔ Espectrograma: si no cambia nada, el audio y espectrograma se reutilizan sin recalcular.
- Se preserva la superposición mínima del contenido detrás de las esquinas curvas del bottom sheet compacto.

---

# Sonidero Fractal v32

**Sonidero Fractal** transforma mundos fractales del plano complejo en sonido mediante una representación tiempo–frecuencia y una reconstrucción iterativa Griffin–Lim.

Autor: **Pedro K. Galindo Vera**

## Inicio rápido

1. Abre `sonidero_fractal_pedro_k_galindo_vera_v32.html` en un navegador moderno.
2. La presentación inicial dura ~3 s y luego desaparece por completo.
3. Desliza el carrusel infinito y toca un mundo fractal.
4. Explora con pan, zoom y, en móvil, rotación con dos dedos.
5. Arrastra el **bottom sheet** hacia arriba para cambiar la configuración cotidiana o técnica.
6. Pulsa **♪ Escuchar** desde el mundo fractal para generar la escucha actual.
7. En el resultado, alterna instantáneamente entre **Fractal** y **Espectrograma** cuando nada cambió; el espectrograma reutiliza la caché.
8. En Espectrograma, usa **▶ / ❚❚** para reproducir o pausar y descarga el WAV desde el sheet intermedio.

Todo el motor vive en un único HTML; no requiere servidor.

## UX v32: un instrumento espacial

La interfaz usa una sola gramática:

- los **gestos manipulan el contenido**;
- el **bottom sheet configura el instrumento**;
- **♪ Escuchar** genera desde el fractal;
- **▶ / ❚❚** reproduce o pausa en el espectrograma.

### Portada y carrusel

La marca aparece desde desenfoque, crece suavemente y desaparece en aproximadamente 3 s. Después quedan solo los mundos.

El carrusel es circular en ambas direcciones. Usa cinco ciclos internos para tolerar flicks rápidos y recentra el ciclo de forma invisible únicamente cuando el momentum termina. Cada tarjeta muestra solo el nombre y una vista más amplia del fractal.

### Mundo fractal

El fractal se trata como una cámara sobre el plano complejo:

\[
(x_c,y_c,s,\theta).
\]

En móvil:

- un dedo: trasladar;
- pellizcar: zoom;
- giro de dos dedos: rotar;
- doble toque: acercar.

La imagen no se estira anisotrópicamente para llenar el viewport.

El contenido continúa aproximadamente **30 px** por detrás del borde superior del bottom sheet compacto. Ese pequeño solapamiento solo rellena visualmente las esquinas redondeadas del sheet; cuando el sheet sube, simplemente cubre el mundo y **no reencuadra ni mueve la cámara**.

La navegación conserva un overscan de aproximadamente **2,5×** para evitar fondos negros mientras se desplaza o rota y luego refina progresivamente.

## Bottom sheet

El mismo componente se usa en el mundo fractal y en el espectrograma. Tiene tres alturas con snap.

### Compacta

Mundo fractal:

- Sonidero Fractal + brújula/orientación;
- **Mundos**;
- alternador **Fractal / Espectrograma**;
- **♪ Escuchar**.

Espectrograma:

- Sonidero Fractal + tiempo actual/total;
- **Mundos**;
- alternador **Fractal / Espectrograma**;
- **▶ Reproducir / ❚❚ Pausa**.

El handle visible sigue siendo pequeño, pero su zona táctil es mayor para que el arrastre sea robusto. El movimiento es 1:1 y al soltar hace snap a compacto, intermedio o expandido.

### Intermedia

Contiene una única configuración cotidiana, sin paneles duplicados:

- presets en una fila horizontal deslizable;
- selector logarítmico de frecuencia mínima y máxima;
- campos exactos de frecuencia;
- rango de energía Fondo ↔ Picos;
- campos exactos en dB;
- duración;
- Griffin–Lim: 4, 8, 16, 32, 64, 128 o 256 iteraciones máximas.

Los presets configuran la escucha completa y no solo la frecuencia.

No existe un botón adicional “Generar”: **Escuchar** es siempre la acción de síntesis.

### Expandida

Mantiene todo lo anterior y añade el laboratorio técnico, sin duplicar duración/frecuencia/energía/Griffin:

- detalle acústico;
- sample rate;
- FFT;
- hop;
- **ventana STFT: Hann, Hamming, Blackman o Rectangular**;
- gamma de magnitud;
- luminancia / bordes / híbrido;
- fase inicial;
- escala visual del espectrograma: Lineal, Log o Mel;
- brillo, contraste, gamma y piso visual;
- paciencia de convergencia;
- mejora mínima porcentual en la ventana;
- tolerancia por iteración.

## Griffin–Lim y parada anticipada

La cadena conceptual es:

\[
\text{fractal}
\rightarrow (M_F,\phi_F)
\rightarrow X_F=M_Fe^{i\phi_F}
\rightarrow \text{Griffin--Lim}
\rightarrow \operatorname{ISTFT}
\rightarrow x[n].
\]

La fase inicial se deriva de la dinámica compleja del fractal. Durante Griffin–Lim se muestran la magnitud objetivo, la STFT realizable actual, la iteración, el error espectral y la mejora.

Además del máximo de iteraciones, la ejecución puede detenerse automáticamente si, durante una ventana configurable —10 iteraciones por defecto—, el error deja de mejorar al porcentaje mínimo configurado.

## Espectrograma

La vista final obedece exactamente al mismo paradigma espacial que el mundo fractal: contenido de pantalla completa + bottom sheet.

El raster ocupa **todo el canvas**. Los ejes están superpuestos:

- tiempo arriba;
- frecuencia a la izquierda;
- etiquetas mayores y legibles;
- la unidad temporal aparece con la última marca, por ejemplo `50 s`;
- la unidad de frecuencia aparece con la etiqueta visible más alta que quepa, por ejemplo `20 kHz`;
- si `22 kHz` colisionaría con la escala temporal, esa etiqueta no se dibuja aunque el espectrograma sí continúe hasta el borde superior.

Gestos:

- un dedo: pan;
- pellizcar: zoom tiempo–frecuencia;
- doble toque: acercar.

No se rota el espectrograma porque sus ejes representan magnitudes físicas.

## Caché y continuidad Fractal ↔ Espectrograma

La escucha guarda una firma de:

- fractal y cámara;
- orientación;
- banda;
- energía;
- duración;
- Griffin–Lim;
- FFT, hop, muestreo y ventana;
- gamma, mapeo y fase;
- criterios de convergencia.

Si se vuelve al fractal y no cambia nada, regresar al espectrograma es inmediato y no recalcula el WAV ni la STFT. Si cambia una región o parámetro relevante, la siguiente acción **Escuchar** reconstruye el audio.

## Integridad del audio

Antes de declarar una generación válida se comprueban:

\[
\mathrm{RMS},\qquad \max|x[n]|,\qquad \mathrm{NaN/Inf},\qquad \text{muestras no nulas}.
\]

La ISTFT usa cobertura completa, overlap-add normalizado y un taper corto en los extremos para evitar clics. La reproducción interactiva usa Web Audio; el WAV PCM se conserva para descarga.

## Arquitectura

- HTML/CSS/JavaScript autocontenido.
- Web Worker para fractales.
- Web Worker para STFT/ISTFT, Griffin–Lim y espectrograma.
- Canvas para fractal y espectrograma.
- Web Audio API para reproducción sincronizada.
- Caché de fractal, audio y matrices espectrales.

## Validación de esta entrega

Se verificó:

- sintaxis JavaScript de todos los bloques `<script>`;
- sintaxis independiente del Worker fractal;
- sintaxis independiente del Worker DSP;
- IDs HTML únicos.

La experiencia táctil final —flick del carrusel, drag del bottom sheet, pinch/rotación y reproducción— debe validarse además en el dispositivo móvil real, porque depende del comportamiento de momentum y gestos del navegador.

## v35 — transición visible al regenerar

Cuando cualquier parámetro cambia, la acción contextual vuelve a **♪ Escuchar**. Al pulsarla desde el espectrograma, Sonidero entra inmediatamente a la pantalla de reconstrucción Griffin–Lim, muestra progreso/convergencia y solo al terminar regresa al espectrograma actualizado. Si nada cambió, el botón sigue funcionando como **▶ Reproducir / ❚❚ Pausa** sin recalcular.
