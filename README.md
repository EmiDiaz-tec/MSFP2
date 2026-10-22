[![Open in MATLAB Online](https://www.mathworks.com/images/responsive/global/open-in-matlab-online.svg)](https://matlab.mathworks.com/open/github/v1?repo=DrPaulValle/MSFBP0)

# Práctica 2 — Modelo del sistema cardiovascular

Repositorio académico de la asignatura **Modelado de Sistemas Fisiológicos**, correspondiente al programa de **Ingeniería Biomédica** del Tecnológico Nacional de México / Instituto Tecnológico de Tijuana. Esta práctica integra el modelado matemático, análisis temporal y de estabilidad, diseño y sintonización de controladores PID, simulación computacional mediante MATLAB/Simulink y su reproducción independiente en Python para el sistema cardiovascular en distintos estados fisiopatológicos.

El caso de estudio representa la dinámica del **sistema cardiovascular** mediante un circuito análogo de parámetros distribuidos. Se busca reproducir, comparar y controlar la respuesta de la presión periférica $P_p(t)$ tomando como referencia la señal de presión aórtica normotensa $P_a(t)$, tanto en lazo abierto como en lazo cerrado para estados de **hipotensión** e **hipertensión**.

**Docente:** Dr. Paul Antonio Valle Trujillo  
**Correo:** paul.valle@tectijuana.edu.mx  
**Departamento:** Ingeniería Eléctrica y Electrónica  
**Institución:** Tecnológico Nacional de México / Instituto Tecnológico de Tijuana  
**Asignatura:** Modelado de Sistemas Fisiológicos  
**Programa:** Ingeniería Biomédica  
**Estudiante:** David Emiliano Díaz de León Bernal · C23211564 · l23211564@tectijuana.edu.mx

**Repositorio de la asignatura:** https://github.com/DrPaulValle/MSF-IBF1020  
**Sede:** Blvd. Alberto Limón Padilla s/n, Tijuana, C. P. 22454, B. C., México.

---

<a id="contenido"></a>
## Contenido

1. [Resumen](#resumen)
2. [Competencia y objetivos](#objetivos)
3. [Relación con el temario](#temario)
4. [Modelo físico y analogía eléctrica](#analogia)
5. [Formulación matemática](#modelo)
6. [Análisis temporal y de estabilidad](#analisis)
7. [Simulación en MATLAB y Simulink](#matlab)
8. [Control en lazo cerrado y sintonización PID](#pid)
9. [Reproducción en Python](#python)
10. [Resultados y verificación cruzada](#resultados)
11. [Flujo de trabajo](#flujo)
12. [Estructura del repositorio](#estructura)
13. [Requisitos](#requisitos)
14. [Referencias](#referencias)

---

<a id="resumen"></a>
## Resumen

La práctica tiene como propósito **analizar, simular y controlar las respuestas hemodinámicas** del sistema cardiovascular en tres condiciones fisiológicas: **Normotenso** (condición de control), **Hipotenso** y **Hipertenso**. El circuito equivalente integra una impedancia característica $Z$, una inductancia inercial $L$, una resistencia periférica $R$ y una compliancia vascular $C$.

A partir de las leyes de Kirchhoff en el dominio del tiempo y de Laplace, se obtiene la función de transferencia del sistema y las ecuaciones integro-diferenciales correspondientes. Se evalúa la estabilidad mediante la ubicación de polos para cada estado hemodinámico y se simula la respuesta ante una señal aórtica real extraída. Finalmente, se diseñan dos **controladores PID con retroalimentación negativa** ($PIDL$ para la condición hipotensa y $PIDH$ para la condición hipertensa) capaces de corregir las desviaciones de presión periférica y acoplarlas a la respuesta normotensa de referencia.

**Palabras clave:** sistema cardiovascular; presión periférica; presión aórtica; normotenso; hipotenso; hipertenso; controlador PID; MATLAB; Simulink; Python; simulación *in silico*.

[Volver al contenido](#contenido)

---

<a id="objetivos"></a>
## Competencia y objetivos

### Competencia a desarrollar

Comprender la dinámica hemodinámica del sistema cardiovascular y aplicar herramientas de Ingeniería de Control para estabilizar y corregir alteraciones de la presión arterial (hipotensión e hipertensión) mediante la caracterización de polos, formulación de modelos matemáticos y diseño de controladores PID computacionales.

### Objetivo general

Modelar, simular y controlar la respuesta de presión periférica $P_p(t)$ ante una señal de entrada aórtica normotensa $P_a(t)$, evaluando el comportamiento en lazo abierto y lazo cerrado para estados hipotensos e hipertensos a través de MATLAB/Simulink y Python.

### Objetivos específicos

- Obtener la función de transferencia $G(s) = \frac{P_p(s)}{P_a(s)}$ utilizando la transformada de Laplace a partir del modelo eléctrico análogo.
- Calcular los polos del sistema para las condiciones normotensa, hipotensa e hipertensa, clasificando la respuesta temporal según su amortiguamiento.
- Evaluar el error en estado estacionario $e_{ss}$ ante una entrada de prueba escalón unitario.
- Simular el modelo en lazo abierto en Simulink empleando el solver `ode15s` y exportar las señales.
- Sintonizar los controladores PID ($PIDL$ y $PIDH$) para cumplir los criterios de desempeño de tiempo de establecimiento ($t_s \le 100\text{ ms}$) y sobreimpulso ($M_p \le 10\%$).
- Reproducir fielmente las señales y respuestas en lazo cerrado utilizando scripts independientes en Python.

[Volver al contenido](#contenido)

---

<a id="temario"></a>
## Relación con el temario

| Unidad | Área | Contenidos aplicados |
|---|---|---|
| I | Sistemas de control retroalimentados | Análisis de la respuesta temporal, estabilidad en lazo abierto, polos y ceros |
| II | Modelado matemático | Analogía del sistema cardiovascular, función de transferencia hemodinámica y ecuaciones integro-diferenciales |
| III | Controladores | Diseños de controladores PID, sintonización $PIDL$ y $PIDH$, corrección de perturbaciones hipotensas e hipertensas |

[Volver al contenido](#contenido)

---

<a id="analogia"></a>
## Modelo físico y analogía eléctrica

El árbol vascular arterial se modela mediante un circuito de parámetros distribuidos RZLC:

| Símbolo | Equivalente eléctrico | Interpretación fisiológica |
|---|---|---|
| $P_a(t)$ | Voltaje de entrada | Presión aórtica |
| $P_p(t)$ | Voltaje de salida | Presión periférica |
| $F_a(t)$ | Corriente de entrada | Flujo sanguíneo aórtico total |
| $Z$ | Resistencia / Impedancia | Impedancia característica de la raíz aórtica |
| $L$ | Inductancia | Inertancia de la masa sanguínea |
| $R$ | Resistencia | Resistencia vascular periférica total (RVT) |
| $C$ | Capacitancia | Compliancia de las paredes arteriales |

### Parámetros por condición fisiológica

| Parámetro | Normotenso (Control) | Hipotenso (Caso 1) | Hipertenso (Caso 2) | Unidad |
|---|---:|---:|---:|---|
| Impedancia ($Z$) | $0.010$ | $0.010$ | $0.010$ | $\mathrm{mmHg}\cdot\mathrm{s}\cdot\mathrm{mL}^{-1}$ |
| Inductancia ($L$) | $0.001$ | $0.001$ | $0.001$ | $\mathrm{mmHg}\cdot\mathrm{s}^2\cdot\mathrm{mL}^{-1}$ |
| Resistencia ($R$) | $1.000$ | $1.000$ | $1.000$ | $\mathrm{mmHg}\cdot\mathrm{s}\cdot\mathrm{mL}^{-1}$ |
| Capacitancia ($C$) | $1.500$ | $3.500$ | $1.000$ | $\mathrm{mL}\cdot(\mathrm{mmHg})^{-1}$ |

[Volver al contenido](#contenido)

---

<a id="modelo"></a>
## Formulación matemática

### 1. Modelo de ecuaciones integro-diferenciales

A partir de las corrientes en los nodos del circuito análogo:

$$F_a(t) = F_L(t) = F_C(t) + F_R(t)$$

$$\frac{P_a(t) - P_p(t)}{Z} + \frac{1}{L}\int [P_a(t) - P_p(t)]\,dt = C \frac{dP_p(t)}{dt} + \frac{P_p(t)}{R}$$

Despejando la salida $P_p(t)$:

$$P_p(t) = \left[ \frac{P_a(t)}{Z} + \frac{1}{L}\int [P_a(t) - P_p(t)]\,dt - C\frac{dP_p(t)}{dt} \right] \frac{RZ}{Z+R}$$

### 2. Función de transferencia

Aplicando la Transformada de Laplace con condiciones iniciales nulas y realizando la reducción algebraica:

$$\boxed{G(s) = \frac{P_p(s)}{P_a(s)} = \frac{LRs + RZ}{CLRZ s^2 + L(Z+R)s + RZ}}$$

[Volver al contenido](#contenido)

---

<a id="analisis"></a>
## Análisis temporal y de estabilidad

### 1. Error en estado estacionario ($e_{ss}$)

Para una entrada escalón unitario $P_a(s) = \frac{1}{s}$:

$$e_{ss} = \lim_{s\to 0} s P_a(s) [1 - G(s)] = \lim_{s\to 0} \left( 1 - \frac{LRs + RZ}{CLRZ s^2 + L(Z+R)s + RZ} \right) = 1 - \frac{RZ}{RZ} = 0$$

El sistema en lazo abierto presenta un error en estado estacionario de $e_{ss} = 0\text{ V}$.

### 2. Estabilidad y cálculo de polos

Los polos del sistema están determinados por la ecuación característica:

$$CLRZ s^2 + L(Z+R)s + RZ = 0$$

$$\lambda_{1,2} = \frac{-L(Z+R) \pm \sqrt{L^2(Z+R)^2 - 4CL R^2 Z^2}}{2CLRZ}$$

* **Hipotenso ($C = 3.5$):**
  $$\lambda_{1,2} = -14.429 \pm 8.805i$$
  *Polos complejos conjugados con parte real negativa $\rightarrow$ Respuesta **subamortiguada** e **inestable/oscilatoria**.*

* **Normotenso ($C = 1.5$):**
  $$\lambda_1 = -12.062, \quad \lambda_2 = -55.272$$
  *Polos reales negativos y distintos $\rightarrow$ Respuesta **sobreamortiguada** y **estable**.*

* **Hipertenso ($C = 1.0$):**
  $$\lambda_1 = -10.000, \quad \lambda_2 = -10.000$$
  *Polos reales negativos e iguales $\rightarrow$ Respuesta **críticamente amortiguada** y **estable**.*

[Volver al contenido](#contenido)

---

<a id="matlab"></a>
## Simulación en MATLAB y Simulink

La simulación se ejecuta desde el script `practica_2.m` cargando el modelo `practica_2.slx` con los siguientes parámetros de simulación:
- **Tiempo de simulación:** $t = 15\text{ s}$
- **Solver:** `ode15s`
- **Paso máximo (`Maxstep`):** $1\times 10^{-2}\text{ s}$

Los datos procesados se exportan a un archivo de datos `signal.xlsx` para la entrada de señal.

```matlab
clc; clear; close all; warning('off','all')
open_system("practica_2.slx")
parameters.Stoptime = '15';
parameters.Solver = 'ode15s';
parameters.Maxstep = '1e-2';
x = sim('practica_2', parameters);
writematrix(x.Pa, 'signal.xlsx');

% Generación de figuras
plotsignals(x.t, x.PpL, x.PpN, x.PpH, 'Individuales');
plotsignals(x.t, x.PpN, x.PpL, x.PIDL, 'Hipotenso');
plotsignals(x.t, x.PpN, x.PpH, x.PIDH, 'Hipertenso');
