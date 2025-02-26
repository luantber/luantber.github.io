---
draft: true 
date: 2024-02-26
categories:
  - Programación
tags:
  - python
---




# Dominando el Desarrollo en Python: Cómo Utilizo Conda y Poetry Juntos para una Gestión Eficiente de Entornos y Dependencias

Como desarrolladores de Python intermedios, todos hemos enfrentado el desafío de gestionar múltiples proyectos con diferentes versiones de Python y dependencias específicas. Asegurar que todo funcione sin conflictos y sea reproducible puede complicarse rápidamente. En este blog, quiero compartir cómo utilizo Conda y Poetry juntos para optimizar mi flujo de trabajo, combinando lo mejor de ambos mundos: la potente gestión de entornos de Conda y la gestión declarativa de dependencias de Poetry. Si buscas una forma práctica y eficiente de organizar tus proyectos, quédate conmigo y te mostraré cómo lo hago paso a paso.

<!-- more -->

## Introducción: ¿Por Qué Conda y Poetry Juntos?
Tanto Conda como Poetry son herramientas que pueden manejar entornos virtuales y dependencias, pero cada una tiene un enfoque distinto. Conda destaca en la creación y gestión de entornos aislados con diferentes versiones de Python, mientras que Poetry brilla en la gestión limpia y declarativa de dependencias. Al usarlas juntas, logro un equilibrio perfecto: entornos robustos y flexibles con Conda, y un control preciso y versionable de dependencias con Poetry. A continuación, exploraremos cada herramienta, sus fortalezas, limitaciones y cómo las integro en mi día a día.

## Entendiendo Conda: El Rey de los Entornos Virtuales
### ¿Qué es Conda?

Conda es un gestor de paquetes y entornos virtuales que permite aislar las dependencias de un proyecto en un entorno específico. Es ideal para trabajar con diferentes versiones de Python y mantener proyectos separados sin conflictos. Para replicar un entorno, exportas un archivo `environment.yml` y lo usas en otro sistema con el comando:

```bash
conda env create -f environment.yml
```

### Fortalezas de Conda
- Gestión de versiones de Python: Puedo crear un entorno con Python 3.8 para un proyecto y otro con 3.10 para otro sin problemas.
- Aislamiento de entornos: Las dependencias de un proyecto no interfieren con las de otro.
- Facilidad de uso: Activar y desactivar entornos con conda activate y conda deactivate es increíblemente sencillo, incluso dentro del mismo proyecto.

!!! tip
    Recomiendo utilizar Miniconda en lugar de Anaconda para instalaciones más ligeras y controladas. Miniconda solo instala lo esencial, permitiéndote añadir solo los paquetes que realmente necesitas.

### Limitaciones de Conda
Sin embargo, Conda no me parece ideal para la gestión de dependencias durante el desarrollo de proyectos. Cuando instalas una dependencia, Conda también instala todas sus subdependencias, y al exportar el archivo environment.yml, este incluye todo, incluso lo que no instalaste directamente. Esto dificulta tener una visión clara de las dependencias reales del proyecto y sus versiones, además de requerir reexportar el archivo constantemente para mantenerlo actualizado.

### Ejemplo de la Limitación
Supongamos que quiero instalar numpy en un entorno:

```bash
conda create --name myenv python=3.8
conda activate myenv
conda install numpy
conda env export > environment.yml
```

El archivo environment.yml resultante podría verse así:

```yaml
name: myenv
channels:
  - defaults
dependencies:
  - numpy=1.21.2
  - mkl=2021.4.0
  - ...
```
Aunque solo necesito numpy, el archivo incluye mkl y otras subdependencias, lo que lo hace menos legible y más difícil de mantener.

## Entendiendo Poetry: El Maestro de las Dependencias
### ¿Qué es Poetry?
Poetry es un gestor de dependencias declarativo, similar a npm en JavaScript. Las dependencias se especifican en un archivo pyproject.toml, que se actualiza automáticamente al instalar o desinstalar paquetes con comandos como poetry add o poetry remove. Este archivo es limpio, versionable con Git y fácil de compartir.

### Fortalezas de Poetry
- Dependencias declarativas: Solo lista las dependencias que instalas directamente, dejando las subdependencias a cargo de Poetry.
- Actualización automática: Al agregar numpy con poetry add numpy, el pyproject.toml se actualiza con la versión instalada.
- Ventajas adicionales:
    - Separa dependencias obligatorias (como fastapi y uvicorn) de las de desarrollo (como black o ruff) mediante grupos.
    - Ofrece flexibilidad en las versiones (puedes bloquearlas o permitir actualizaciones).
    - Resuelve dependencias para garantizar compatibilidad total.
    - Facilita la creación de paquetes con package-mode=true.
    
### Limitaciones de Poetry
Aunque Poetry puede gestionar entornos virtuales, no es su fuerte. Activar un entorno requiere poetry shell o ejecutar comandos con poetry run, lo que no es tan intuitivo como Conda. Además, para una gestión más avanzada de entornos, podrías necesitar plugins como poetry-plugin-shell, lo que añade complejidad.

### Ejemplo de la Limitación
Para trabajar en un proyecto con Poetry:

```bash
poetry new myproject
cd myproject
poetry shell  # Activar el entorno
# O usar:
poetry run python my_script.py
```

Comparado con la simplicidad de conda activate, este proceso puede sentirse menos fluido, especialmente si cambias frecuentemente entre entornos.

## Cómo Uso Conda y Poetry Juntos

La clave de mi flujo de trabajo es usar Conda para gestionar entornos virtuales y Poetry para gestionar dependencias. Esto me da lo mejor de ambos: entornos aislados y flexibles con Conda, y un archivo pyproject.toml limpio y declarativo con Poetry.

### Flujo de Trabajo Práctico
1. Crear un entorno con Conda:

```bash
conda create --name myproject python=3.8
conda activate myproject
```

2. Inicializar Poetry en el proyecto:
```bash
poetry init
```
3. Agregar dependencias con Poetry:
```bash
poetry add numpy
```
El pyproject.toml se actualiza solo con numpy, sin subdependencias.

4. Replicar el entorno en otra máquina:
Crear el entorno Conda:

```bash
conda create --name myproject python=3.8
conda activate myproject
```

Instalar las dependencias con Poetry:
```bash
poetry install
```
**Nota:** Conda facilita la creación de entornos reutilizables. Por ejemplo, mantengo un entorno data-dev con el stack de ciencia de datos (numpy, pandas, sklearn) para experimentos rápidos en cualquier directorio.

## Ventajas Adicionales de Esta Combinación

- Claridad: El pyproject.toml solo muestra mis dependencias directas, mientras Conda maneja el entorno.
- Flexibilidad: Puedo tener entornos reutilizables o específicos por proyecto.
- Compatibilidad: Poetry asegura que todas las dependencias sean compatibles, y Conda me da independencia del Python del sistema.
- Independencia del sistema: Conda no depende del Python del sistema operativo, evitando conflictos.
- Dependencias de desarrollo: Poetry permite separar fastapi (obligatoria) de black (desarrollo), manteniendo el proyecto organizado.
- Resolución de dependencias: Poetry garantiza compatibilidad total, rechazando instalaciones si no encuentra una solución viable.
- Creación de paquetes: Poetry simplifica la publicación de bibliotecas con package-mode=true.

**Nota:** Otra ventaja es la integración con VSCode: los entornos de Conda se detectan automáticamente, permitiéndote seleccionarlos fácilmente desde la barra inferior para agilizar tu trabajo.

## Conclusión
Usar Conda y Poetry juntos ha transformado cómo gestiono mis proyectos en Python. Conda me da control total sobre entornos y versiones de Python, mientras que Poetry mantiene mis dependencias organizadas y compartibles. Esta combinación es perfecta para desarrolladores intermedios que buscan un flujo de trabajo eficiente y reproducible. Te invito a probar este enfoque en tu próximo proyecto: ¡verás cómo simplifica tu vida como desarrollador!