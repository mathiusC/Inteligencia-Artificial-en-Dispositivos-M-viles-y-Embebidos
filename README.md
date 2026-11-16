# Clasificación de mascarillas con TensorFlow Lite en Raspberry Pi

Proyecto del curso **Inteligencia Artificial en Dispositivos Móviles y Embebidos**.

El programa abre la cámara, toma una foto y, con **tu propio modelo** `.tflite`, dice si la persona
tiene mascarilla (`Withmask`) o no (`Withoutmask`).

---

## Archivos del repositorio

| Archivo | Para qué sirve |
|---|---|
| `Requirements.txt` | Comandos para preparar la Raspberry Pi (se ejecutan **una sola vez**). |
| `comandos_terminal` | Comandos para ejecutar el proyecto cada vez que lo uses. |
| `proyectofinaltflite.py` | El programa: toma la foto y hace la predicción con tu modelo. |
| `my_model.tflite` | **Tu modelo.** No viene en el repositorio: lo pones tú (ver paso 3). |

---

## Requisitos

- Raspberry Pi con **Raspberry Pi OS de 64 bits**. Probado en la versión basada en Debian 13 (Trixie),
  que trae **Python 3.13**: se usa ese mismo Python, no hace falta instalar otro.
- Una cámara USB.
- Conexión a internet (TensorFlow se descarga con `pip`).

---

## Paso 1. Descargar el proyecto

```bash
git clone https://github.com/mathiusC/Inteligencia-Artificial-en-Dispositivos-M-viles-y-Embebidos
cd Inteligencia-Artificial-en-Dispositivos-M-viles-y-Embebidos
```

## Paso 2. Preparar la Raspberry Pi (solo la primera vez)

Abre `Requirements.txt` y ejecuta sus líneas **en orden**, copiando y pegando en la terminal,
**dentro de la carpeta del proyecto**. Lo que hacen:

1. Actualizan el sistema e instalan librerías con `apt-get` (pide tu contraseña de `sudo`).
   Si preguntan `¿Desea continuar? [S/n]`, responde **S**.
2. Crean el entorno virtual `tf-env` con el Python de la Raspberry.
3. Activan el entorno e instalan `tensorflow`, `opencv-python` y `matplotlib` con `pip`.

En total tarda unos **20 minutos** (lo que más demora es `apt-get upgrade`).

Para comprobar que quedó bien:

```bash
source tf-env/bin/activate
python -c "import tensorflow as tf; print(tf.__version__)"
```

## Paso 3. Poner tu modelo

Copia tu modelo entrenado en la carpeta del proyecto con el nombre exacto **`my_model.tflite`**
(junto a `proyectofinaltflite.py`).

Tu modelo debe:

- recibir imágenes de **224 × 224** píxeles con 3 canales (el programa las divide entre 255);
- tener **2 salidas** en este orden: `['Withmask', 'Withoutmask']`.

## Paso 4. Ejecutar (cada vez que lo uses)

Son los comandos de `comandos_terminal`:

```bash
cd ~/Inteligencia-Artificial-en-Dispositivos-M-viles-y-Embebidos
source tf-env/bin/activate
python proyectofinaltflite.py
```

> Ejecútalo siempre **desde la carpeta del proyecto**: el programa busca `my_model.tflite`
> y guarda las fotos en la carpeta donde estás.

### Cómo se usa

1. Se abre una ventana con la cámara.
2. Presiona **ESPACIO** para tomar una foto. Se guarda como `opencv_frame_0.png`
   (si tomas más, `opencv_frame_1.png`, `opencv_frame_2.png`, ...).
3. Presiona **ESC** para cerrar la cámara.
4. El programa predice con **`opencv_frame_0.png`** (la primera foto), muestra el resultado en la terminal
   y abre una ventana con la foto y su clase como título.

---

## Para investigar: detección de caras

En el código hay líneas **comentadas** que usan un detector de caras de OpenCV (*haarcascade*)
para dibujar un rectángulo verde (con mascarilla) o rojo (sin mascarilla) alrededor de la cara.
Están comentadas **a propósito**: tu tarea es investigar cómo activarlas.

Pista: OpenCV ya trae el archivo del detector, en `cv2.data.haarcascades + 'haarcascade_frontalface_default.xml'`.
Si descomentas el `for (x, y, w, h) in faces:` sin crear antes `faces`, obtendrás un `NameError`.

---

## Problemas comunes

| Problema | Solución |
|---|---|
| `No module named 'tensorflow'` | No activaste el entorno: `source tf-env/bin/activate`. |
| `No module named 'imp'` | El entorno tiene un paquete `flatbuffers` viejo. Con el entorno activado: `pip install --upgrade flatbuffers`. |
| `externally-managed-environment` al usar `pip` | Estás fuera del entorno: actívalo con `source tf-env/bin/activate`. |
| No encuentra `my_model.tflite` | Revisa el nombre del archivo y que estés en la carpeta del proyecto (`cd ...`). |
| `failed to grab frame` | La cámara no está conectada o no es la número 0. Conecta una cámara USB. |
| Error al leer `opencv_frame_0.png` | No tomaste ninguna foto: presiona ESPACIO antes de ESC. |
