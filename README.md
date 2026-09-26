# Simulador de Electrodo de pH con ESP32

**Repositorio del Laboratorio N.º 1 — Acondicionamiento de señales de alta impedancia**  
*Curso: Instrumentación Biomédica III — Escuela Profesional de Ingeniería Biomédica, UNMSM*

---

## Descripción

Este proyecto simula el comportamiento eléctrico de un electrodo de pH real utilizando un microcontrolador ESP32. El sistema recibe un valor de pH (0–14) por el Monitor Serial, calcula el potencial teórico según la ecuación de Nernst a 25 °C, y lo entrega como una señal analógica real a través del DAC del pin **GPIO25**.

Esta señal simulada se usó como fuente de alta impedancia para estudiar experimentalmente el efecto de carga (*loading error*) y su corrección mediante un buffer de ganancia unitaria (seguidor de voltaje), implementado con dos amplificadores operacionales distintos: el **TL084** (entrada JFET) y el **LM324** (entrada bipolar).

---

## ¿Cómo funciona el código?

1. El usuario ingresa un valor de pH (entre 0 y 14) por el Monitor Serial.
2. El ESP32 calcula el potencial de Nernst:
   $$\text{E} = -0.05916 \times (\text{pH} - 7)$$
3. La señal se escala (factor 5.0) y se le aplica un offset de 1.65 V para mantenerla dentro del rango seguro del DAC (0 V – 3.3 V).
4. El valor resultante se convierte a un valor digital de 8 bits y se envía al DAC en **GPIO25** mediante `dacWrite()`.
5. El Monitor Serial muestra el pH ingresado, el voltaje de Nernst, el voltaje final entregado y el valor digital correspondiente.

---

## Archivo principal

| Archivo | Descripción |
| :--- | :--- |
| `simulador_ph.ino` | Sketch de Arduino/ESP32 que genera la señal simulada de pH descrita arriba. |

---

## Montaje experimental

El laboratorio se desarrolló en tres etapas:
* **Etapa 1:** Verificación directa del voltaje generado por el DAC (GPIO25 → osciloscopio/multímetro).
* **Etapa 2:** Se intercaló una resistencia $R_1 = 1\text{ M}\Omega$ entre el ESP32 y el instrumento de medición (Nodo A), simulando la alta impedancia de un electrodo real y evidenciando el efecto de carga.
* **Etapa 3:** Se conectó el Nodo A a la entrada no inversora de un buffer seguidor de voltaje (TL084 y, por separado, LM324), midiendo la señal corregida en el Nodo B.

---

## Resultados obtenidos

### Resumen Consolidado de Datos de Laboratorio

| Etapa | pH simulado | V teórico (V) | V osciloscopio (V) | V multímetro (V) | Buffer TL084 (V) | Buffer LM324 (V) |
| :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| **1** | 4 | 2.5374 | 2.436 | — | — | — |
| **1** | 7 | 1.6500 | 1.583 | — | — | — |
| **1** | 10 | 0.7626 | 0.772 | — | — | — |
| **2** | 4 | 2.5374 | 1.160 | 1.165 | — | — |
| **2** | 7 | 1.6500 | 0.800 | 0.758 | — | — |
| **2** | 10 | 0.7626 | 0.360 | 0.364 | — | — |
| **3** | 4 | 2.5374 | — | — | 2.469 | 2.475 |
| **3** | 7 | 1.6500 | — | — | 1.602 | 1.623 |
| **3** | 10 | 0.7626 | — | — | 0.793 | 0.813 |

> **Nota:** Sin buffer, el error de carga respecto al valor teórico alcanzó entre **51% y 54%**. Con el buffer TL084 el error se redujo a un rango de **2.69% a 3.99%**, y con el LM324 a un rango de **1.64% a 6.61%**, confirmando la efectividad de un buffer de alta impedancia de entrada para mitigar el efecto de carga sobre fuentes de alta impedancia.

---

## Componentes utilizados

* ESP32 (placa de desarrollo)
* CI TL084N (op-amp cuádruple, entrada JFET)
* CI LM324N (op-amp cuádruple, entrada bipolar)
* Resistencia $1\text{ M}\Omega$
* Capacitores cerámicos $0.1\ \mu\text{F}$ (desacoplo de alimentación)
* Fuente de alimentación dual $\pm 9\text{ V}$
* Osciloscopio y multímetro digital

---

## Integrantes

* Dávila Pucuhuayla, Jazmín Sarai
* Leon Vasquez, Jimmy Fabricio
* More Quispe, Gregory Martin
* Maldonado Villacorta, Paola Margot

**Docente:** María Elisia Armas Alvarado  
**Curso:** Instrumentación Biomédica III — Escuela Profesional de Ingeniería Biomédica, UNMSM
