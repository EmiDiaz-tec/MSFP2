[![Open in MATLAB Online](https://www.mathworks.com/images/responsive/global/open-in-matlab-online.svg)](https://matlab.mathworks.com/open/github/v1?repo=DrPaulValle/MSFBP0)

# Práctica 2 — Modelo del sistema cardiovascular

Repositorio académico de la asignatura **Modelado de Sistemas Fisiológicos**, correspondiente al programa de **Ingeniería Biomédica** del Tecnológico Nacional de México / Instituto Tecnológico de Tijuana. Esta práctica integra el modelado matemático, análisis temporal y de estabilidad, diseño y sintonización de controladores PID, simulación computacional mediante MATLAB/Simulink y su reproducción independiente en Python para el sistema cardiovascular en distintos estados fisiopatológicos.

El caso de estudio representa la dinámica del **sistema cardiovascular** mediante el modelo **Windkessel de cuatro elementos** ($Z$, $L$, $R$, $C$), un circuito eléctrico equivalente de parámetros concentrados y de segundo orden. Se busca reproducir, comparar y controlar la respuesta de la presión periférica $P_p(t)$ ante la señal de presión aórtica $P_a(t)$, tanto en lazo abierto como en lazo cerrado para estados de **hipotensión** e **hipertensión**, tomando al individuo normotenso como referencia.

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

La práctica tiene como propósito **analizar, simular y controlar las respuestas hemodinámicas** del sistema cardiovascular en tres condiciones fisiológicas: **Normotenso** (condición de control), **Hipotenso** y **Hipertenso**. El circuito equivalente integra una impedancia característica $Z$, una inductancia inercial $L$, una resistencia periférica $R$ y una compliancia vascular $C$. A pesar de tener cuatro elementos, el modelo contiene solo dos elementos independientes de almacenamiento de energía ($C$ y $L$), por lo que es un sistema de **segundo orden**.

A partir de las leyes de Kirchhoff en el dominio del tiempo y de Laplace, se obtiene la función de transferencia del sistema y las ecuaciones integro-diferenciales correspondientes. Se evalúa la estabilidad mediante la ubicación de polos para cada estado hemodinámico y se simula la respuesta ante una señal aórtica real extraída. Los tres individuos difieren únicamente en la compliancia $C$. Finalmente, se diseñan dos **controladores PID con retroalimentación negativa** ($PIDL$ para la condición hipotensa y $PIDH$ para la condición hipertensa) capaces de corregir las desviaciones de presión periférica y acoplarlas a la respuesta normotensa de referencia.

**Palabras clave:** sistema cardiovascular; modelo Windkessel; presión periférica; presión aórtica; normotenso; hipotenso; hipertenso; controlador PID; MATLAB; Simulink; Python; simulación *in silico*.

[Volver al contenido](#contenido)

---

<a id="objetivos"></a>
## Competencia y objetivos

### Competencia a desarrollar

Comprender la dinámica hemodinámica del sistema cardiovascular y aplicar herramientas de Ingeniería de Control para estabilizar y corregir alteraciones de la presión arterial (hipotensión e hipertensión) mediante la caracterización de polos, formulación de modelos matemáticos y diseño de controladores PID computacionales.

### Objetivo general

Modelar, simular y controlar la respuesta de presión periférica $P_p(t)$ ante una señal de entrada aórtica $P_a(t)$, evaluando el comportamiento en lazo abierto y lazo cerrado para estados hipotensos e hipertensos a través de MATLAB/Simulink y Python.

### Objetivos específicos

- Identificar entrada, salida, flujos y parámetros del circuito equivalente del sistema arterial.
- Obtener la función de transferencia $G(s) = \frac{P_p(s)}{P_a(s)}$ utilizando la transformada de Laplace a partir del modelo eléctrico análogo.
- Formular el modelo de ecuaciones integro-diferenciales y su representación en variables de estado con $\mathbf{x}(t)=[F_L(t);\,P_p(t)]^T$.
- Calcular los polos del sistema para las condiciones normotensa, hipotensa e hipertensa, clasificando la respuesta temporal según su amortiguamiento.
- Evaluar el error en estado estacionario $e_{ss}$ ante una entrada de prueba escalón unitario.
- Simular el modelo en lazo abierto en Simulink empleando el solver `ode15s` y exportar las señales.
- Definir las señales de error $e_{hipo}(t)$ y $e_{hiper}(t)$ respecto al individuo normotenso.
- Sintonizar los controladores PID ($PIDL$ y $PIDH$) para cumplir los criterios de desempeño de tiempo de establecimiento ($t_s \le 100\text{ ms}$) y sobreimpulso ($M_p \le 10\%$).
- Reproducir fielmente las señales y respuestas en lazo cerrado utilizando scripts independientes en Python.
- Documentar los resultados en un repositorio de GitHub.

[Volver al contenido](#contenido)

---

<a id="temario"></a>
## Relación con el temario

| Unidad | Área | Contenidos aplicados |
|---|---|---|
| I | Sistemas de control retroalimentados | Diagramas de bloques, señal de error, lazo abierto y cerrado, análisis de la respuesta temporal, error en estado estacionario, estabilidad, polos y ceros |
| II | Modelado matemático | Analogía circuito–sistema arterial, leyes de Kirchhoff, función de transferencia hemodinámica y ecuaciones integro-diferenciales |
| III | Controladores | Diseño de controladores PID, sintonización $PIDL$ y $PIDH$, corrección de perturbaciones hipotensas e hipertensas |

[Volver al contenido](#contenido)

---

<a id="analogia"></a>
## Modelo físico y analogía eléctrica

Las características hemodinámicas del sistema arterial (resistencia periférica total, distensibilidad arterial, impedancia característica de la aorta proximal e inertancia de la sangre) se estudian mediante el modelo Windkessel. El modelo original (atribuido a Otto Frank) usa una sección distensible y una resistencia periférica; se amplió con la impedancia característica de la región proximal y, finalmente, con una inertancia que representa la masa de sangre en movimiento, lo que da el **modelo de cuatro elementos**.

La analogía se establece mediante las correspondencias **presión ↔ voltaje** y **flujo sanguíneo ↔ corriente**:

| Símbolo | Equivalente eléctrico | Interpretación fisiológica |
|---|---|---|
| $P_a(t)$ | Voltaje de entrada | Presión aórtica |
| $P_p(t)$ | Voltaje de salida (nodo del capacitor) | Presión periférica / asociada a la distensibilidad arterial |
| $F_a(t)$ | Corriente de entrada | Flujo sanguíneo aórtico total |
| $F_Z(t)$ | Corriente en $Z$ | Flujo por la impedancia característica |
| $F_L(t)$ | Corriente en $L$ | Flujo por la inertancia arterial |
| $F_C(t)$ | Corriente en $C$ | Flujo que almacena volumen por distensión |
| $F_R(t)$ | Corriente en $R$ | Flujo por la resistencia periférica |
| $Z$ | Resistencia / Impedancia | Impedancia característica de la raíz aórtica |
| $L$ | Inductancia | Inertancia de la masa sanguínea |
| $R$ | Resistencia | Resistencia vascular periférica total (RVT) |
| $C$ | Capacitancia | Compliancia de las paredes arteriales |

**Topología:** $Z$ y $L$ están en paralelo entre $P_a$ y el nodo $P_p$; de ese nodo a tierra se conectan en paralelo $C$ y $R$. Por tanto $F_a = F_Z + F_L = F_C + F_R$.

### Parámetros por condición fisiológica

| Parámetro | Normotenso (Control) | Hipotenso (Caso 1) | Hipertenso (Caso 2) | Unidad |
|---|---:|---:|---:|---|
| Impedancia ($Z$) | $0.010$ | $0.010$ | $0.010$ | $\mathrm{mmHg}\cdot\mathrm{s}\cdot\mathrm{mL}^{-1}$ |
| Inductancia ($L$) | $0.001$ | $0.001$ | $0.001$ | $\mathrm{mmHg}\cdot\mathrm{s}^2\cdot\mathrm{mL}^{-1}$ |
| Resistencia ($R$) | $1.000$ | $1.000$ | $1.000$ | $\mathrm{mmHg}\cdot\mathrm{s}\cdot\mathrm{mL}^{-1}$ |
| Capacitancia ($C$) | $1.500$ | $3.500$ | $1.000$ | $\mathrm{mL}\cdot(\mathrm{mmHg})^{-1}$ |

Las diferencias se establecen modificando solo $C$. Un $C$ mayor (hipotenso) representa un sistema arterial con mayor capacidad de distensión, lo que tiende a atenuar los incrementos de presión de la entrada. Un $C$ menor (hipertenso) representa menor distensibilidad y mayor rigidez arterial, de modo que una variación de volumen produce cambios de presión más pronunciados.

> **Nota sobre unidades:** fisiológicamente las presiones están en mmHg; en las simulaciones y figuras las señales se grafican en voltios, de acuerdo con la analogía eléctrica.

[Volver al contenido](#contenido)

---

<a id="modelo"></a>
## Formulación matemática

### 1. Ecuaciones principales

$$F_a(t) = F_Z(t) + F_L(t) = F_C(t) + F_R(t)$$

con

$$F_Z(t)=\frac{P_a(t)-P_p(t)}{Z},\quad F_L(t)=\frac{1}{L}\int [P_a(t)-P_p(t)]\,dt,\quad F_C(t)=C\frac{dP_p(t)}{dt},\quad F_R(t)=\frac{P_p(t)}{R}$$

### 2. Modelo de ecuaciones integro-diferenciales

Sustituyendo en $F_Z + F_L = F_C + F_R$:

$$\frac{P_a(t) - P_p(t)}{Z} + \frac{1}{L}\int [P_a(t) - P_p(t)]\,dt = C \frac{dP_p(t)}{dt} + \frac{P_p(t)}{R}$$

Despejando la salida $P_p(t)$:

$$P_p(t) = \left[ \frac{P_a(t)}{Z} + \frac{1}{L}\int [P_a(t) - P_p(t)]\,dt - C\frac{dP_p(t)}{dt} \right] \frac{RZ}{Z+R}$$

La única incógnita es la salida $P_p(t)$, y no puede despejarse de los términos donde aparece derivada o integrada; por eso, en el diagrama de bloques, $P_p$ se retroalimenta a esos términos.

### 3. Función de transferencia

Aplicando la Transformada de Laplace con condiciones iniciales nulas:

$$\frac{P_a(s)-P_p(s)}{Z} + \frac{P_a(s)-P_p(s)}{Ls} = C s P_p(s) + \frac{P_p(s)}{R}$$

$$\left(\frac{1}{Z}+\frac{1}{Ls}\right)P_a(s) = \left(Cs+\frac{1}{R}+\frac{1}{Z}+\frac{1}{Ls}\right)P_p(s)$$

$$\boxed{G(s) = \frac{P_p(s)}{P_a(s)} = \frac{LRs + RZ}{CLRZ\, s^2 + L(Z+R)s + RZ}}$$

El denominador es de segundo grado por los dos elementos de almacenamiento ($C$ y $L$).

### 4. Representación en variables de estado

Con $\mathbf{x}(t)=[F_L(t);\,P_p(t)]^T$ y teniendo en cuenta que $F_C = F_Z + F_L - F_R$:

$$\dot{x}_1(t) = \frac{P_a(t)-x_2(t)}{L}$$

$$\dot{x}_2(t) = \frac{1}{C}\left[x_1(t) + \frac{P_a(t)-x_2(t)}{Z} - \frac{x_2(t)}{R}\right],\qquad y(t)=x_2(t)=P_p(t)$$

Tomando la transformada de Laplace de este sistema se recupera la misma $G(s)$.

[Volver al contenido](#contenido)

---

<a id="analisis"></a>
## Análisis temporal y de estabilidad

### 1. Error en estado estacionario ($e_{ss}$)

Para una entrada escalón unitario $P_a(s) = \frac{1}{s}$:

$$e_{ss} = \lim_{s\to 0} s P_a(s) [1 - G(s)] = \lim_{s\to 0} \left( 1 - \frac{LRs + RZ}{CLRZ s^2 + L(Z+R)s + RZ} \right) = 1 - \frac{RZ}{RZ} = 0$$

La ganancia en continua es $G(0)=1$ para los tres individuos, de modo que el sistema en lazo abierto presenta un error en estado estacionario de $e_{ss} = 0\text{ V}$.

### 2. Estabilidad y cálculo de polos

Los polos del sistema están determinados por la ecuación característica:

$$CLRZ s^2 + L(Z+R)s + RZ = 0$$

$$\lambda_{1,2} = \frac{-L(Z+R) \pm \sqrt{L^2(Z+R)^2 - 4CL R^2 Z^2}}{2CLRZ}$$

Con $Z=0.010$, $L=0.001$ y $R=1.000$:

| Individuo | $C$ | Denominador | Polos | Comportamiento |
|---|---:|---|---|---|
| Hipotenso | 3.500 | $3.5\times10^{-5}s^2+0.00101s+0.01$ | $-14.429 \pm 8.805i$ | Estable, **subamortiguado** |
| Normotenso | 1.500 | $1.5\times10^{-5}s^2+0.00101s+0.01$ | $-12.062,\; -55.272$ | Estable, **sobreamortiguado** |
| Hipertenso | 1.000 | $1.0\times10^{-5}s^2+0.00101s+0.01$ | $-11.127,\; -89.873$ | Estable, **sobreamortiguado** |

Los tres individuos tienen polos con parte real negativa, por lo que **son estables**. Los polos complejos del hipotenso producen una respuesta oscilatoria que se amortigua (no es inestabilidad). Como $Z$, $L$ y $R$ no cambian, el numerador $LR(s+Z/L)$ es el mismo en los tres casos y aporta un **cero** en $s=-Z/L=-10$.

[Volver al contenido](#contenido)

---

<a id="matlab"></a>
## Simulación en MATLAB y Simulink

La simulación se ejecuta desde el script `practica_2.m` cargando el modelo `practica_2.slx` con los siguientes parámetros de simulación:
- **Tiempo de simulación:** $t = 15\text{ s}$
- **Solver:** `ode15s`
- **Paso máximo (`Maxstep`):** $1\times 10^{-2}\text{ s}$
- **Condiciones iniciales:** nulas

Los datos procesados se exportan a un archivo de datos `signal.xlsx` para la entrada de señal. El modelo entrega al Workspace las señales: `t`, `Pa`, `PpN` (normotenso), `PpL` (hipotenso), `PpH` (hipertenso), `PIDL` (hipotenso con PID) y `PIDH` (hipertenso con PID).

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
```

### Señales de error

Para cada caso se define una señal de error independiente respecto al individuo de referencia:

$$e_{hipo}(t) = P_{p,norm}(t) - P_{p,hipo}(t),\qquad e_{hiper}(t) = P_{p,norm}(t) - P_{p,hiper}(t)$$

### Comprobación independiente: modelo de EDOs con Euler explícito (opcional)

Como verificación de la simulación de Simulink, el modelo en variables de estado puede integrarse directamente en MATLAB con **Euler explícito** y $\Delta t = 10^{-3}\text{ s}$, con condiciones iniciales nulas ($F_L(0)=0$, $P_p(0)=0$). La entrada escalonada de Simulink se interpola a ese paso con `interp1(...,'previous')`:

$$F_L[k+1] = F_L[k] + \frac{P_a[k]-P_p[k]}{L}\Delta t$$

$$P_p[k+1] = P_p[k] + \left[\frac{F_L[k]}{C} - \left(\frac{1}{ZC}+\frac{1}{RC}\right)P_p[k] + \frac{P_a[k]}{ZC}\right]\Delta t$$

Con ese paso y polos de hasta $\approx -90\ \mathrm{s}^{-1}$, el producto paso por polo es $\approx 0.09$, por lo que el método explícito es numéricamente estable en este caso. *(Si no se implementa en esta entrega, eliminar esta subsección.)*

[Volver al contenido](#contenido)

---

<a id="pid"></a>
## Control en lazo cerrado y sintonización PID

### 1. Retroalimentación negativa

La referencia es la presión del normotenso, así que el error del lazo es

$$e(t) = P_{p,norm}(t) - P_{p,caso}(t)$$

y la función de transferencia de lazo cerrado, con $K(s)$ el controlador y $G(s)$ la planta del caso, es

$$T(s) = \frac{K(s)G(s)}{1+K(s)G(s)}$$

Esquema de control (cada caso se analiza de forma independiente):

1. La señal de entrada alimenta al individuo normotenso (control) y, en paralelo, al paciente (caso).
2. La salida del normotenso es la referencia del lazo.
3. En el sumador se resta (retroalimentación negativa) la salida del paciente con tratamiento.
4. El error pasa por el PID, que alimenta al paciente.
5. Las señales se envían a un osciloscopio (scope) para compararlas.

### 2. Criterios de diseño

- Tiempo de establecimiento: $t_s \le 100\text{ ms}$
- Sobreimpulso: $M_p \le 10\%$
- Error en estado estacionario nulo.

### 3. Ganancias del controlador

| Caso | $k_P$ | $k_I$ | $k_D$ |
|---|---:|---:|---:|
| Hipotenso ($PIDL$) | 460.710 | 1 122 930.282 | 0.00626 |
| Hipertenso ($PIDH$) | 129.995 | 312 844.959 | 0.00174 |

> **Procedencia:** estas ganancias provienen de la sintonización de L. Z. Moreno Zavala (mismo modelo, mismos parámetros y mismos criterios) y fueron verificadas de forma independiente con una simulación de escalón unitario del lazo cerrado (sección siguiente).

### 4. Métricas obtenidas

| Caso | $t_s$ (ms) | $M_p$ (%) | $e_{ss}$ (V) | ¿Cumple? |
|---|---:|---:|---:|---|
| Hipotenso con $PIDL$ | ≈ 1.0 | ≈ 9.9 | 0 | Sí |
| Hipertenso con $PIDH$ | ≈ 1.0 | ≈ 9.6 | 0 | Sí |

Métricas obtenidas con escalón unitario en $T(s)=KG/(1+KG)$, usando el PID realizado con componentes (sección 6); $t_s$ con banda de 2 %. Con el PID ideal con $k_P$ nominal, el sobreimpulso del hipotenso sube a ≈ 10.4 %, ligeramente por encima del criterio, por lo que conviene usar la realización con componentes (o revisar la sintonización en Simulink).

### 5. Polos en lazo cerrado

Con un PID, la ecuación característica de lazo cerrado es de tercer orden. Se recomienda calcular los polos de cada caso (por ejemplo con `pole(feedback(K*G,1))` en MATLAB) y verificar que todos tengan parte real negativa. Conviene revisar si algún polo de lazo cerrado queda cerca del cero de la planta en $s=-10$, ya que se produciría una cancelación casi exacta.

| Caso | Polos de lazo cerrado |
|---|---|
| Hipotenso | $-8254.84,\; -3296.98,\; -9.99997$ |
| Hipertenso | $-8294.19,\; -3212.83,\; -9.99997$ |

Los tres polos tienen parte real negativa en ambos casos (lazo cerrado estable). Un polo queda prácticamente en $s=-10$, sobre el cero de la planta ($-Z/L$), con el que casi se cancela; los otros dos son mucho más rápidos que los de la planta en lazo abierto.

### 6. Realización con amplificadores operacionales (opcional)

El PID se implementa con amplificadores operacionales, con $C_r = 1\ \mu\mathrm{F}$ fijo y los demás elementos calculados a partir de las ganancias:

$$R_e = \frac{1}{k_I C_r},\qquad R_r = k_P R_e,\qquad C_e = \frac{k_D}{R_r}$$

$$K(s)=\frac{R_eR_rC_eC_r\,s^2+(R_eC_e+R_rC_r)\,s+1}{R_eC_r\,s}$$

Al dividir algebraicamente, $K(s)=k_{P,ef}+\frac{k_I}{s}+k_Ds$ con $k_{P,ef}=\frac{R_r}{R_e}+\frac{C_e}{C_r}$.

| Caso | $R_e$ | $R_r$ | $C_e$ | $k_{P,ef}$ (vs. nominal) |
|---|---:|---:|---:|---|
| Hipotenso | 0.8905 Ω | 410.27 Ω | 15.26 µF | ≈ 475.97 (460.710) |
| Hipertenso | 3.196 Ω | 415.53 Ω | 4.187 µF | ≈ 134.18 (129.995) |

Si se calcula $R_r=k_PR_e$, la ganancia proporcional efectiva resulta ligeramente **mayor** que la nominal; hay que tenerlo presente al comparar con los valores sintonizados en Simulink.

[Volver al contenido](#contenido)

---

<a id="python"></a>
## Reproducción en Python

El script de Python reproduce los resultados con una segunda herramienta. Sus apartados son:

1. **Lectura de la entrada y vector de tiempo:** carga `signal.xlsx`, construye $t\in[0,15]$ con $\Delta t=10^{-2}$ s ($N=1501$ muestras) y remuestrea la señal con `scipy.signal.resample`.
2. **Función `cardio(Z,L,R,C)`:** construye $G(s)$ con `num=[L*R, R*Z]` y `den=[C*L*R*Z, L*R+L*Z, R*Z]`, y calcula los polos con `np.roots`.
3. **Tres individuos:** hipotenso, normotenso e hipertenso, imprimiendo función de transferencia y polos.
4. **Respuestas en lazo abierto:** `ctrl.forced_response` para cada individuo.
5. **Función del controlador:** arma el PID y cierra el lazo con `ctrl.feedback(..., sign=-1)`.
6. **Lazo cerrado:** respuesta del hipotenso y del hipertenso con PID usando como referencia la salida del normotenso.

Para ejecutarlo:

```bash
pip install numpy control matplotlib scipy pandas openpyxl
python practica_2.py
```

El archivo `signal.xlsx` debe estar en la misma carpeta que el script (se genera desde MATLAB).

[Volver al contenido](#contenido)

---

<a id="resultados"></a>
## Resultados y verificación cruzada

### Archivos de resultados

| Figura | MATLAB/Simulink | Python |
|---|---|---|
| Individuos en lazo abierto | `Individuales_matlab.pdf` | `Individuales_python.pdf` |
| Normotenso, hipotenso y PID | `Hipotenso_matlab.pdf` | `Hipotenso_python.pdf` |
| Normotenso, hipertenso y PID | `Hipertenso_matlab.pdf` | `Hipertenso_python.pdf` |
| Modelo de EDOs (Euler), opcional | `odes_matlab.pdf` | — |

> Los PDF se enlazan para su consulta porque GitHub no los presenta como imágenes incrustadas.

`[COMPLETAR: enlazar o incrustar tus figuras exportadas]`

### Aspectos para discutir

- Diferencias entre las respuestas de los tres individuos en lazo abierto y su relación con $C$ y con el tipo de amortiguamiento de cada uno.
- Efecto del PID: cuánto se acerca la respuesta del hipotenso y del hipertenso a la del normotenso.
- Consistencia entre las figuras de MATLAB y de Python.
- Cumplimiento del error en estado estacionario nulo, de $t_s$, $M_p$ y de la estabilidad en lazo cerrado.
- Limitaciones del modelo Windkessel de cuatro elementos y de la analogía eléctrica.

### Notas de verificación

- **Entrada de los casos:** según el esquema de control, el paciente (caso) recibe directamente la señal de entrada $P_a(t)$, no la salida del normotenso. Confirmar que tanto Simulink como Python usen la misma entrada para los tres individuos.
- **Entrada interpolada vs. remuestreada:** si se usa la integración de EDOs en MATLAB (`interp1` con `'previous'`) y Python usa `scipy.signal.resample`, pueden aparecer diferencias pequeñas entre figuras.
- **Leyendas:** verificar que cada curva de `plotsignals` esté rotulada con el individuo correcto (en la rama "Hipertenso" la segunda curva es `x.PpH`).
- **Escalas y colores:** verificar que los ejes y la asignación de colores sean consistentes entre MATLAB y Python, o documentar las diferencias.
- **Amortiguamiento:** el hipotenso es subamortiguado; el normotenso y el hipertenso son sobreamortiguados (polos reales distintos, no críticamente amortiguados).

### Interpretación propia

`[COMPLETAR: comentario interpretativo de cada familia de respuestas (lazo abierto, hipotenso con PID, hipertenso con PID), con tus observaciones fisiológicas, matemáticas y de control]`

[Volver al contenido](#contenido)

---

<a id="flujo"></a>
## Flujo de trabajo

### Etapa 1. Formulación y análisis
- [ ] Identificar en el circuito $P_a, P_p, F_a, F_Z, F_L, F_C, F_R, Z, L, R, C$.
- [ ] Derivar las ecuaciones integro-diferenciales.
- [ ] Obtener la función de transferencia $P_p(s)/P_a(s)$.
- [ ] Expresar el modelo en variables de estado.
- [ ] Calcular el error en estado estacionario ante escalón unitario.
- [ ] Calcular los polos de los tres individuos y clasificar su amortiguamiento.

### Etapa 2. Simulación de los individuos
- [ ] Ejecutar `practica_2.slx` con $t_{end}=15$ s, `ode15s` y `Maxstep = 1e-2`.
- [ ] Exportar la entrada a `signal.xlsx`.
- [ ] Graficar las respuestas de los tres individuos en lazo abierto.

### Etapa 3. Diseño y comprobación del controlador
- [ ] Definir las señales de error $e_{hipo}$ y $e_{hiper}$.
- [ ] Implementar el PID con retroalimentación negativa para cada caso.
- [ ] Registrar $k_P$, $k_I$ y $k_D$ y verificar $t_s \le 100$ ms y $M_p \le 10\%$.
- [ ] Graficar las respuestas de lazo cerrado en MATLAB y exportarlas a PDF.
- [ ] Reproducir las respuestas en Python con las mismas ganancias.

### Etapa 4. Documentación
- [ ] Comparar MATLAB y Python.
- [ ] Redactar una interpretación de los resultados y sus diferencias.
- [ ] Publicar los archivos y un `CITATION.cff` correctamente identificados en GitHub.

[Volver al contenido](#contenido)

---

<a id="estructura"></a>
## Estructura del repositorio

```text
.
├── README.md                          # Reporte y guía de reproducción
├── CITATION.cff                       # Datos de citación del repositorio
├── practica_2.m                       # Script / cuaderno MATLAB
├── practica_2.slx                     # Modelo Simulink
├── practica_2.py                      # Implementación en Python
├── Analisis_Matematico.pdf            # Desarrollo analítico a mano
├── signal.xlsx                        # Entrada Pa(t) exportada desde Simulink
├── Individuales_matlab.pdf
├── Hipotenso_matlab.pdf
├── Hipertenso_matlab.pdf
├── Individuales_python.pdf
├── Hipotenso_python.pdf
└── Hipertenso_python.pdf
```

Si se usan otros nombres o subcarpetas, actualizar las rutas del README.

[Volver al contenido](#contenido)

---

<a id="requisitos"></a>
## Requisitos

### Software

- MATLAB y Simulink, con el modelo `practica_2.slx` y el script de la práctica.
- Python (por ejemplo, con Visual Studio Code) y las bibliotecas `numpy`, `control`, `matplotlib`, `scipy`, `pandas` y `openpyxl` (esta última para leer `signal.xlsx`).
- Git y GitHub para presentar el repositorio.

Las versiones mínimas del software no se especifican en el instructivo.

### Secuencia de reproducción

1. Consultar el repositorio general de la asignatura y descargar los archivos de la práctica.
2. Verificar los valores de $Z$, $L$, $R$, $C$ de cada individuo y las condiciones iniciales.
3. Derivar el modelo y completar el análisis de error y polos.
4. Ejecutar `practica_2.m`: simulación, exportación de `signal.xlsx` y figuras en PDF.
5. Colocar `signal.xlsx` junto al script de Python y ejecutarlo para obtener las tres figuras.
6. Comparar las figuras de MATLAB y Python.
7. Completar este README con las evidencias propias y publicar el repositorio junto con su `CITATION.cff`.

### Resultados mínimos esperados

| Entregable | Evidencia solicitada |
|---|---|
| Formulación matemática | Ecuaciones, función de transferencia y modelo en variables de estado |
| Análisis del sistema | Error en estado estacionario, polos y estabilidad de los tres individuos |
| Simulación | Modelo `.slx` y respuestas en lazo abierto |
| Control | Ganancias del PID y respuestas en lazo cerrado de hipotenso e hipertenso |
| Python | Código `.py` y tres figuras |
| Documentación | `README.md`, `CITATION.cff` e interpretación de los resultados |

### Criterios de presentación académica

Debe distinguirse entre resultados de referencia reproducidos y aportes o modificaciones propias. La ejecución de scripts y la generación de figuras no sustituyen la discusión del comportamiento fisiológico, matemático y de control del sistema.

[Volver al contenido](#contenido)

---

<a id="referencias"></a>
## Referencias

[1] P. A. Valle, *Práctica: Modelado y control del sistema cardiovascular mediante el modelo Windkessel (sección 5.4)*, instructivo de la asignatura Modelado de Sistemas Fisiológicos, Tecnológico Nacional de México / Instituto Tecnológico de Tijuana.

[2] M. C. Khoo, *Physiological Control Systems: Analysis, Simulation, and Estimation*, 2nd ed., IEEE Press, 2018.

[3] F. C. Hoppensteadt y C. S. Peskin, *Modeling and Simulation in Medicine and the Life Sciences*, Springer.

[4] J. E. Hall, *Guyton and Hall Textbook of Medical Physiology*, 14th ed., Elsevier, 2021.

[5] K. Ogata, *Ingeniería de Control Moderna*, 5.ª ed., Pearson Educación, 2010.

[6] N. S. Nise, *Control Systems Engineering*, 8th ed., John Wiley & Sons, 2020.

[7] P. A. Valle, *Syllabus para Modelado de Sistemas Fisiológicos*, Tecnológico Nacional de México / Instituto Tecnológico de Tijuana, Tijuana, B. C., México, 2026. Disponible en: https://biomath.xyz/course/

[8] Tecnológico Nacional de México, *Programa de estudio de la asignatura Modelado de Sistemas Fisiológicos*, IBF-1020.

---

## Uso académico

Este repositorio se utiliza como evidencia de una práctica de Modelado de Sistemas Fisiológicos. Su finalidad es desarrollar habilidades de modelado, análisis, simulación y diseño de control, y de documentación técnica; cualquier cambio de parámetros, estructura del controlador o procedimiento debe identificarse y justificarse.
