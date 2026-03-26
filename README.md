# UD03 — Integración de Vídeo Web

> Módulo: **Diseño de Interfaces Web** | Ciclo DAW | Esteban Ospina

---

## Descripción

Página web que integra un vídeo optimizado y convertido desde formato MOV a formatos
compatibles con la web (MP4 y WebM), con controles nativos HTML5 y efectos CSS interactivos.

---

## Estructura de archivos

```
video-ud03/
├── index.html   → Página web con el reproductor
├── styles.css   → Hoja de estilos enlazada
├── video.mp4    → Vídeo optimizado (formato principal)
├── video.webm   → Vídeo alternativo (mejor compresión)
├── poster.jpg   → Imagen de previsualización
└── README.md    → Este documento explicativo
```

---

## Paso 1 — Archivo original

- **Formato:** MOV (QuickTime / Apple)
- **Problema:** Tamaño elevado, compatibilidad limitada en navegadores web
- **Solución:** Compresión + conversión con HandBrake

---

## Paso 2 — Optimización con HandBrake

**Herramienta:** HandBrake (gratuita) — https://handbrake.fr

### Configuración aplicada

| Parámetro      | Valor                    | Por qué                              |
| -------------- | ------------------------ | ------------------------------------ |
| Preset         | Web → Gmail Large 720p30 | Punto de partida equilibrado         |
| Codec de vídeo | H.264 (x264)             | Máxima compatibilidad web            |
| Resolución     | 1280 × 720 px            | 720p suficiente para web             |
| Framerate      | 30 FPS                   | Estándar web, fluido                 |
| Quality (RF)   | 24                       | Equilibrio calidad/tamaño            |
| Codec de audio | AAC                      | Compatible con todos los navegadores |
| Audio bitrate  | 128 kbps                 | Calidad aceptable, tamaño reducido   |

### Resultado de la optimización

| Versión          | Formato | Tamaño aprox. | Calidad   |
| ---------------- | ------- | ------------- | --------- |
| Original         | MOV     | ~100 MB       | Alta      |
| Optimizado MP4   | H.264   | ~15–20 MB     | Muy buena |
| Alternativo WebM | VP9     | ~12–16 MB     | Muy buena |

**Reducción aproximada: 80–85% del tamaño original** sin pérdida visual perceptible.

---

## Paso 3 — Conversión a formatos web

### MP4 (H.264) — formato principal

- Codec: H.264
- Compatibilidad: Chrome, Firefox, Safari, Edge, iOS, Android
- Uso: Primera fuente en el `<video>`

### WebM (VP9) — formato alternativo

- Codec: VP9
- Compatibilidad: Chrome, Firefox, Edge, Android Chrome
- Ventaja: ~20% mejor compresión que MP4
- Limitación: Safari (desktop e iOS) no lo soporta → por eso el MP4 va primero

### Conversión WebM con HandBrake o FFmpeg

Con HandBrake: seleccionar preset **WebM VP9** en la pestaña Video.

Con FFmpeg (opcional, línea de comandos):

```bash
ffmpeg -i video.mp4 -c:v libvpx-vp9 -crf 33 -b:v 0 -c:a libopus video.webm
```

---

## Paso 4 — Integración HTML

```html
<video
  class="video-player"
  controls
  preload="metadata"
  poster="poster.jpg"
  aria-label="Vídeo de demostración"
>
  <source src="video.mp4" type="video/mp4" />
  <source src="video.webm" type="video/webm" />
  <p>
    Tu navegador no soporta HTML5. <a href="video.mp4">Descarga el vídeo</a>.
  </p>
</video>
```

### Atributos clave explicados

| Atributo              | Función                                                                                                    |
| --------------------- | ---------------------------------------------------------------------------------------------------------- |
| `controls`            | Muestra los controles nativos: play, pausa, volumen, barra de progreso, pantalla completa                  |
| `preload="metadata"`  | El navegador carga solo los metadatos (duración, dimensiones), no el vídeo completo. Mejora el rendimiento |
| `poster="poster.jpg"` | Imagen mostrada antes de que el usuario pulse play                                                         |
| `<source>` múltiples  | El navegador elige automáticamente el primer formato que soporta                                           |
| Texto fallback        | Se muestra si el navegador no soporta `<video>` (navegadores muy antiguos)                                 |

---

## Paso 5 — Compatibilidad verificada

| Navegador / Dispositivo | MP4 (H.264) | WebM (VP9) | Controles | Resultado       |
| ----------------------- | ----------- | ---------- | --------- | --------------- |
| Chrome 124 — Windows    | ✓           | ✓          | ✓         | ✅ OK           |
| Firefox 125 — Windows   | ✓           | ✓          | ✓         | ✅ OK           |
| Safari 17 — macOS       | ✓           | —          | ✓         | ✅ OK (usa MP4) |
| Edge 124 — Windows      | ✓           | ✓          | ✓         | ✅ OK           |
| Chrome — Android        | ✓           | ✓          | ✓         | ✅ OK           |
| Safari — iOS            | ✓           | —          | ✓         | ✅ OK (usa MP4) |

**Conclusión:** Todos los navegadores reproducen el vídeo correctamente.
Safari selecciona MP4 automáticamente al no soportar WebM.

---

## Paso 6 — Efectos interactivos CSS

| Efecto                 | Elemento               | CSS aplicado                                 |
| ---------------------- | ---------------------- | -------------------------------------------- |
| Vídeo "flota" al hover | `.video-wrapper:hover` | `transform: translateY(-3px)` + `box-shadow` |
| Borde acento al hover  | `.video-wrapper:hover` | `box-shadow` con color acento                |
| Icono play decorativo  | `.video-overlay`       | `opacity: 0` → `opacity: 1` en hover         |
| Zoom del icono         | `.overlay-icon svg`    | `transform: scale(1.1)` en hover             |
| Brillo del vídeo       | `.video-player:hover`  | `filter: brightness(1.05)`                   |
| Hover en tarjetas      | `.step-card:hover`     | `translateY(-2px)` + borde acento            |
| Hover en filas tabla   | `tr:hover`             | `background` suave                           |
| Hover en metadatos     | `.meta-item:hover`     | `border-color` acento                        |

---

## Decisiones técnicas

**¿Por qué MP4 antes que WebM en el `<source>`?**
Safari (desktop e iOS) no soporta WebM/VP9. Si WebM fuera primero, Safari no podría reproducir el vídeo. Al poner MP4 primero, todos los navegadores lo encuentran como fallback seguro.

**¿Por qué `preload="metadata"` y no `preload="auto"`?**
Con `auto`, el navegador descarga el vídeo completo al cargar la página aunque el usuario no lo vaya a ver. Con `metadata` solo descarga la información básica, ahorrando ancho de banda.

**¿Por qué `poster`?**
Sin poster, el vídeo muestra un fotograma negro o el primer frame hasta que el usuario interactúa. El poster mejora la experiencia visual inmediatamente.

---

## Cómo visualizar

1. Coloca `video.mp4`, `video.webm` y `poster.jpg` en la misma carpeta que `index.html`
2. Abre VS Code → clic derecho en `index.html` → **Open with Live Server**
3. URL: `http://127.0.0.1:5500/index.html`

---

## Autor

**Esteban Ospina** — DAW · Diseño de Interfaces Web · Madrid 2025
