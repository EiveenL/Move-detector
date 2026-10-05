# Detector de movimiento con visión por computadora

Detector de movimiento en tiempo real construido con **Python y OpenCV**. Captura el video de la cámara, identifica zonas con cambio entre fotogramas consecutivos, delimita el objeto en movimiento y dispara una alerta sonora automática.

![Python](https://img.shields.io/badge/Python-3.8%2B-blue)
![OpenCV](https://img.shields.io/badge/OpenCV-cv2-green)
![Licencia](https://img.shields.io/badge/licencia-MIT-lightgrey)

---

## Qué resuelve

La detección de movimiento por diferencia de fotogramas es la base de aplicaciones de monitoreo: vigilancia de zonas restringidas, detección de anomalías en líneas de proceso, conteo de objetos y activación de alertas sin intervención humana. Este proyecto implementa el pipeline completo desde cero, sin librerías de alto nivel que oculten la lógica.

## Cómo funciona

El algoritmo procesa cada par de fotogramas en seis etapas:

| # | Etapa | Función OpenCV | Propósito |
|---|-------|----------------|-----------|
| 1 | Diferencia absoluta | `cv2.absdiff()` | Aísla los píxeles que cambiaron entre dos fotogramas consecutivos |
| 2 | Escala de grises | `cv2.cvtColor()` | Reduce a un solo canal para simplificar el procesamiento |
| 3 | Desenfoque gaussiano | `cv2.GaussianBlur()` (kernel 5×5) | Suaviza el ruido del sensor y evita falsos positivos |
| 4 | Umbralización binaria | `cv2.threshold()` (umbral 20) | Convierte a blanco y negro: movimiento vs. fondo |
| 5 | Dilatación | `cv2.dilate()` (3 iteraciones) | Rellena huecos y une regiones fragmentadas |
| 6 | Detección de contornos | `cv2.findContours()` | Identifica las regiones en movimiento |

**Filtrado por área mínima.** Solo se consideran contornos con un área superior a 4000 px². Este umbral descarta el ruido residual, cambios de iluminación y movimientos irrelevantes, que es la principal causa de falsos positivos en este tipo de sistemas.

Cada región válida se marca con un rectángulo delimitador (`cv2.boundingRect`) y dispara la reproducción asíncrona de una alerta sonora.

## Estructura

```
Move-detector/
├── main.py                # Lógica completa de detección
└── alarm-door-chime.wav   # Sonido de alerta
```

## Requisitos

```bash
pip install opencv-python imutils
```

> `winsound` es parte de la librería estándar de Python en Windows. Para ejecutar en Linux o macOS, reemplázalo por `playsound` o `simpleaudio`.

## Ejecución

```bash
python main.py
```

Se abre una ventana con el video en vivo y los recuadros sobre las zonas en movimiento. Presiona **`q`** para salir.

## Parámetros ajustables

| Parámetro | Valor actual | Efecto al modificarlo |
|-----------|--------------|------------------------|
| Umbral binario | `20` | Más bajo = mayor sensibilidad, más falsos positivos |
| Área mínima de contorno | `4000` | Más bajo = detecta objetos más pequeños o más lejanos |
| Iteraciones de dilatación | `3` | Más alto = agrupa regiones cercanas en una sola detección |
| Kernel de desenfoque | `(5, 5)` | Más grande = más suavizado, menos sensible al detalle fino |

## Posibles extensiones

- Grabar automáticamente un clip de video cuando se detecte movimiento.
- Enviar una notificación (correo, Telegram) en lugar de la alerta sonora local.
- Definir zonas de interés (ROI) para ignorar áreas con movimiento constante.
- Sustituir la diferencia de fotogramas por un sustractor de fondo (`cv2.createBackgroundSubtractorMOG2`) para escenas con iluminación variable.

## Licencia

MIT
