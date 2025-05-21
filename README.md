# Urbanite: Sistema de Ayuda al Aparcamiento Basado en Ultrasonidos

## Authors

* **Álvaro San Emeterio Valdés** - email: [a.sanemeterio@alumnos.upm.es](mailto:a.sanemeterio@alumnos.upm.es)
* **Alejandro Barrio Domínguez** - email: [alejandro.barrio.dominguez@alumno.es](mailto:alejandro.barrio.dominguez@alumno.es)

## Descripción del proyecto

**Urbanite** es un sistema empotrado desarrollado sobre la plataforma Nucleo-STM32F446RE que permite detectar la distancia a obstáculos utilizando un sensor de ultrasonidos HC-SR04. Esta información se muestra al usuario mediante un LED RGB, y se controla el funcionamiento mediante un botón físico. El sistema está implementado en lenguaje C y sigue una arquitectura modular basada en máquinas de estados finitos (FSM) y principios de programación portable y reutilizable.

## Diagrama del proyecto

![Diagrama de elementos del sistema](docs/assets/imgs/urbanite_diagram.png)

> El sistema central con la placa Nucleo-STM32 aloja el microcontrolador STM32F446RE. Se encarga de gestionar el encendido y apagado del sistema Urbanite, y de interpretar la distancia para actuar en consecuencia.

> La placa Nucleo-STM32 también tiene el botón de usuario B1, y el LED de usuario LD2. Estos conforman el sub-sistema de control básico. El botón se usará para detectar pulsaciones que el sistema central interpretará para encender/apagar o pausar el display. El LED podrá usarse para saber si hemos realizado correctamente una operación, a modo de feedback.

> El sub-sistema de medida de distancia representa un dispositivo HW que incorpora un transceptor de ultrasonidos HC-SR04. Este dispositivo contiene un altavoz que emite un pulso ultrasónico y un micrófono que recoge el eco. La distancia a la que se encuentra un obstáculo se puede calcular a partir del tiempo que tarda en llegar el eco.

> El módulo de actuación está compuesto por un LED RGB que se encenderá en función de la distancia.

---

## Version 1 - Botón de usuario

### Descripción general

Esta versión implementa el control básico del sistema mediante un botón de usuario (PC13). Se usa para encender, apagar y pausar el sistema Urbanite. Se emplea una máquina de estados finita (FSM) para gestionar los estados del botón y se aplica lógica anti-rebote por software.

### Funcionalidad implementada

- Lectura del botón mediante interrupciones (EXTI13).
- FSM para detección de pulsación y duración.
- Gestión del estado del sistema según duración de la pulsación.
- Temporización con SysTick.
- Tests unitarios para parte `PORT` y `COMMON`.
- Documentación generada con Doxygen.

### Estructura modular

- **PORT**: acceso al hardware del botón.
- **COMMON**: lógica genérica y FSM del botón.

### Diagrama de estados FSM BUTTON

![FSM del botón de usuario](docs/assets/imgs/fsm_button.png)

---

## Version 2 - Transceptor de ultrasonidos

### Descripción general

En esta versión se implementa la librería para el sensor de ultrasonidos HC-SR04. Se emplean tres temporizadores (TIM2, TIM3, TIM5) para gestionar respectivamente:
- El pulso de trigger.
- La medida de la señal de eco (input capture).
- El timeout entre mediciones.

### Funcionalidad implementada

- Configuración del trigger por temporizador.
- Medición de eco con input capture.
- Temporizador de timeout entre medidas.
- FSM para gestionar el ciclo de medida.
- Tests unitarios para las tres señales (trigger, echo y timeout).

### Estructura modular

- **PORT**:
  - Control del pin de trigger.
  - Lectura del pin de echo (input capture).
  - Configuración de temporizadores.
- **COMMON**:
  - FSM que controla el ciclo de medida.
  - Cálculo de distancia con fórmula estándar.

### Diagrama Trigger/Echo

![Diagrama de temporización HC-SR04](docs/assets/imgs/ultrasound_timing.png)

---

## Version 3 - Display RGB

### Descripción general

En esta versión se implementa el subsistema de actuación mediante un LED RGB de cátodo común. Se emplean temporizadores configurados en modo PWM (Pulse Width Modulation) para controlar la intensidad de cada uno de los tres colores (Rojo, Verde, Azul), permitiendo generar colores combinados en función de la distancia detectada.

Se utiliza una FSM para gestionar la visualización de colores de forma escalonada según la distancia.

### Funcionalidad implementada

- Generación de señales PWM mediante timers hardware.
- Control de la intensidad de los colores RGB mediante modulación de ciclo de trabajo.
- Gestión de visualización según distancia medida.
- Test unitarios para la librería de control del LED RGB.
- Documentación generada con Doxygen.

### Estructura modular

- **PORT**:
  - Configuración de los timers PWM.
  - Control del duty cycle para cada color.
- **COMMON**:
  - FSM de visualización en función de la distancia.
  - Lógica de encendido/apagado de colores.

### Diagrama de señales PWM

![Ejemplo de señales PWM para color amarillento](docs/assets/imgs/pwm_signals_example.png)

> Ejemplo de cómo varían las señales PWM para generar colores compuestos en el LED RGB.

### Montaje del LED RGB

![Montaje LED RGB con resistencias](docs/assets/imgs/rgb_led_setup.png)

> Fotografía o esquema del montaje del LED RGB con resistencias conectado a la placa Nucleo.

---

## Version 4 - Modos de bajo consumo e integración final

### Descripción general

En esta versión se integra todo el sistema Urbanite completo: control por botón, medición de distancia mediante ultrasonidos y visualización mediante el display RGB. Se implementan modos de bajo consumo para optimizar el gasto energético cuando el sistema no está activo.

Se desarrolla una FSM principal que coordina todos los subsistemas anteriores.

### Funcionalidad implementada

- Integración de las FSM de botón, sensor de ultrasonidos y display RGB en una única FSM de sistema.
- Implementación de modos de bajo consumo (Stop Mode y Sleep Mode) para reducir el consumo energético.
- Gestión del encendido, apagado y pausa del sistema de forma centralizada.
- Reanudación correcta del sistema tras despertar de modo de bajo consumo.
- Test de integración para verificar el correcto funcionamiento de todo el sistema.

### Estructura modular

- **PORT**:
  - Configuración de modos de bajo consumo.
- **COMMON**:
  - FSM principal del sistema Urbanite que coordina las FSM individuales de cada subsistema.

### Diagrama de la FSM Urbanite

![FSM del sistema Urbanite](docs/assets/imgs/fsm_urbanite.png)

> Diagrama de la FSM que integra los modos ON, OFF, PAUSE y modos de bajo consumo.

### Montaje final del sistema

![Montaje final del sistema Urbanite](docs/assets/imgs/final_system_setup.png)

> Fotografía del montaje final mostrando todos los elementos del sistema funcionando conjuntamente.

---

## Recursos adicionales

- [Documentación generada con Doxygen](docs/html/index.html)
- [Repositorio oficial Urbanite - UPM](https://github.com/sdg2DieUpm/urbanite)
- [Tutoriales y vídeos SDG2](https://www.youtube.com/channel/UCYIw_gl745WMJ1n0MamDzQw)

---

## Licencia

Este proyecto está basado en material docente de la Universidad Politécnica de Madrid (UPM).  
**No está permitida la venta o distribución sin autorización de los autores.**

---

