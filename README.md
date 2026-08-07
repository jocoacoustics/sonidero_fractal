# Sonidero Fractal v15
### por Pedro K. Galindo Vera

https://jocoacoustics.github.io/sonidero_fractal/

**Sonidero Fractal** es una experiencia web interactiva para **explorar fractales del plano complejo**, seleccionar una región, convertirla en sonido y visualizar el espectrograma resultante.

La aplicación está diseñada como **una sola experiencia responsive**:

- en **PC** mantiene una interfaz amplia con paneles laterales y visualización grande;
- en **móvil** adapta el flujo a pantallas táctiles con controles más grandes, paneles plegables y barras a ancho completo.

---

## Estado actual

Esta versión se concentra **solo en fractales del plano complejo**.

### Fractales incluidos

- **Mandelbrot**
- **Julia**
- **Burning Ship**
- **Tricorn**
- **Multibrot**
- **Newton**

Cada fractal tiene:

- miniatura propia;
- vista general;
- lugares destacados o presets de exploración;
- zoom y selección de región;
- generación de audio y espectrograma.

---

## Características principales

### 1) Selección del fractal
La pantalla inicial usa un **carril horizontal centrado con efecto peek** para descubrir los fractales disponibles.

### 2) Exploración del fractal
- zoom por selección de caja;
- acercar / alejar / vista completa;
- historial de vistas anterior y siguiente;
- presets de regiones famosas;
- recálculo progresivo con barra de avance.

### 3) Sonificación
- definición de banda de frecuencias;
- escalas **lineal**, **logarítmica** y **mel**;
- presets rápidos: voz, aves, insectos, murciélagos y banda completa;
- control simple de **rango de energía**;
- perfiles de sonido;
- estimación de tiempo de generación.

### 4) Visualización final
- reproducción del WAV;
- espectrograma real calculado a partir del audio generado;
- zoom y paneo sobre el espectrograma;
- controles visuales de brillo, contraste, gamma y piso visual;
- posibilidad de volver al paso del fractal o al paso del sonido.

---

## Experiencia responsive

## En PC
- layout ancho;
- fractal y espectrograma grandes;
- paneles laterales y controles visibles;
- navegación cómoda con mouse, rueda y teclado.

## En móvil
- mismo HTML, sin versión separada;
- botones más grandes;
- barras de frecuencia y energía a ancho completo;
- paneles inferiores plegables;
- soporte táctil para arrastre, selección y zoom;
- controles simplificados para una experiencia más limpia.

---

## Cómo usar

1. Abre `sonidero_fractal_pedro_k_galindo_vera_v15.html` en un navegador moderno.
2. Elige un fractal.
3. Explora una región o continúa con la vista actual.
4. Configura la banda de frecuencias y el perfil sonoro.
5. Genera el audio.
6. Escucha el resultado y analiza su espectrograma.

---

## Publicación rápida en GitHub Pages

Sube estos archivos a un repositorio:

- `sonidero_fractal_pedro_k_galindo_vera_v15.html`
- `README.md`

Si quieres que GitHub Pages sirva la app directamente como página principal, puedes:

- renombrar el HTML a `index.html`, o
- mantener el nombre y enlazarlo desde un `index.html` simple.

### Opción mínima
Renombra:

```bash
mv sonidero_fractal_pedro_k_galindo_vera_v15.html index.html
```

Luego activa **GitHub Pages** desde la rama principal.

---

## Estructura técnica

La aplicación está contenida en **un solo archivo HTML** y utiliza:

- **Canvas 2D** para fractales y espectrograma;
- **Web Workers** para cálculo fractal y procesamiento DSP;
- **síntesis e inversión espectral aproximada** para convertir imagen en audio;
- caché en memoria para vistas recientes del fractal y resultados.

---

## Compatibilidad recomendada

Probado y pensado para navegadores modernos, especialmente:

- Chrome
- Edge
- Chromium
- navegadores móviles basados en WebKit / Chromium

Para mejor rendimiento se recomienda un equipo con varios núcleos y navegador actualizado.

---

## Roadmap inmediato

Próximas mejoras probables:

- refinamiento adicional de UX móvil;
- más presets de regiones por fractal;
- optimización de caché e historial;
- más control visual y sonoro sin entrar al laboratorio;
- futuras familias fractales fuera del plano complejo.

---

## Autoría

**Pedro K. Galindo Vera**

Experiencia conceptual, dirección y enfoque del proyecto.

---

## Licencia

A definir por el autor.
