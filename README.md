# Reporte Técnico: Separador de Residuos Automatizado

Este documento detalla la arquitectura de hardware, la integración de periféricos y la configuración del entorno de software basados en la Raspberry Pi 5 para el proyecto de separación de residuos mediante visión artificial.

## 1. Arquitectura del Sistema en Raspberry Pi 5

La Raspberry Pi 5 actúa como la unidad central de procesamiento del sistema, encargada de la ejecución de algoritmos de inteligencia artificial, el control de actuadores mecánicos y la gestión de la conectividad en la nube.

* **Visión Artificial:** Procesamiento en tiempo real utilizando OpenCV y el modelo optimizado YOLOv8n para clasificar los residuos en categorías de orgánicos e inorgánicos.
* **Control de Actuadores:** Gestión de pines GPIO para enviar señales de control a servomotores y motores a pasos equipados con controladores A4988, responsables de la desviación física de los residuos.
* **Trazabilidad y Autenticación:** Integración del módulo RFID MFRC522 mediante el bus SPI para la identificación de usuarios y la asignación de puntos.
* **Persistencia de Datos:** Conexión con Google Firebase para actualizar el estado del sistema y los perfiles de usuario en formato JSON.

---

## 2. Configuración del Entorno Linux

A continuación se describen los comandos esenciales ejecutados en la consola de comandos de Linux (Raspberry Pi OS) para preparar el sistema operativo y sus respectivas interfaces de comunicación.

### 2.1. Actualización y Preparación del Sistema

| Comando | Función |
| :--- | :--- |
| `sudo apt update` | Sincroniza la lista de paquetes disponibles desde los repositorios oficiales. |
| `sudo apt upgrade -y` | Instala las versiones más recientes de todos los paquetes del sistema actualmente instalados. |
| `sudo raspi-config` | Herramienta de configuración del sistema que permite habilitar periféricos críticos como SPI, I2C y la interfaz de la cámara. |

### 2.2. Verificación de Interfaces y Hardware

| Comando | Función |
| :--- | :--- |
| `lsmod / grep spi` | Confirma que el módulo del kernel para la comunicación SPI esté cargado correctamente (esencial para el lector RFID). |
| `ls /dev/video*` | Lista los dispositivos de video conectados para asegurar que la cámara sea reconocida por el sistema (típicamente `/dev/video0`). |
| `pinout` | Proporciona una representación gráfica de la distribución física de los pines GPIO en la placa. |
| `gpio readall` | Muestra el estado detallado, modo de operación (entrada/salida) y niveles de voltaje de cada pin GPIO. |

### 2.3. Despliegue del Entorno Virtual de Python y Dependencias

Dada la arquitectura de la Raspberry Pi 5, el aislamiento de dependencias es mandatorio para garantizar la estabilidad del software.

| Comando | Función |
| :--- | :--- |
| `sudo apt install python3-venv python3-pip -y` | Instala las herramientas nativas para la creación de entornos virtuales y la gestión de paquetes de Python. |
| `python3 -m venv env_separador` | Genera el directorio del entorno virtual aislado denominado `env_separador`. |
| `source env_separador/bin/activate` | Modifica las variables de entorno de la sesión actual de la terminal para usar el intérprete de Python aislado. |
| `pip install ultralytics opencv-python` | Instala el framework de Ultralytics (YOLOv8) y la biblioteca central de OpenCV para el procesamiento de imágenes. |
| `pip install spidev mfrc522` | Instala los controladores de Python requeridos para interactuar con el bus SPI y el chip RFID. |
| `pip install firebase-admin` | Descarga e instala el SDK de administración de Google Firebase para permitir la sincronización de la base de datos distribuida. |

---

## 3. Operación y Diagnóstico en Tiempo Real

Para la puesta en marcha del prototipo, se ejecutan las rutinas de inicialización y el monitoreo de recursos en el sistema operativo Linux:

* **Inicialización del script principal:** Tras activar el entorno virtual con `source env_separador/bin/activate`, se invoca el archivo principal mediante el comando `python3 main.py`, lo cual arranca concurrentemente el hilo de captura de video, el bucle de detección de YOLOv8n, la escucha del lector RFID y la actualización del archivo JSON local y remoto.
* **Diagnóstico de rendimiento del procesador:** Debido a la carga computacional implícita en la inferencia de redes neuronales, se utiliza la utilidad `htop` en una terminal secundaria para supervisar de manera activa la distribución de carga en los núcleos de la Raspberry Pi 5 y el consumo de la memoria RAM.

README.md
Mostrando README.md.
