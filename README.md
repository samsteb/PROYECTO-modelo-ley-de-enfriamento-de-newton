# Modelo de temperatura (Ley de enfriamiento de Newton)

Notebooks de Jupyter que simulan cómo cambia la temperatura de un objeto en contacto con un ambiente. Resuelven la ecuación diferencial de forma numérica y analítica, calculan el punto de equilibrio con su autovalor y generan las gráficas de series de tiempo, campo de direcciones, línea de fase y variaciones de los parámetros.

ESTUDIANTES:
Samuel Esteban Rojas Amézquita - 160005133
Cristian David Garcia Villamil - 160005114

## Modelo matemático

```
dT/dt = -k·(T - Ta)      con T(0) = T0
Solución exacta:  T(t) = Ta + (T0 - Ta)·e^(-k·t)
```

| Parámetro | Significado | Valor por defecto |
|---|---|---|
| `k` | Constante de transferencia de calor (1/min) | 0.15 |
| `T_amb` (Ta) | Temperatura del ambiente (°C) | 20.0 |
| `T0` | Temperatura inicial del objeto (°C) | 90.0 |

## Requisitos

- Python 3.9 o superior.
- Las dependencias listadas en `requirements.txt` (se instalan con un solo comando, ver más abajo).

## Instalación

1. Descarga el proyecto:
   ```bash
   git clone <URL-de-tu-repositorio>
   ```
   O descarga el ZIP desde GitHub y descomprímelo.
2. Crea un entorno virtual (recomendado):
   ```bash
   python -m venv .env
   ```
3. Actívalo:
   - Windows: `.env\Scripts\activate`
   - Linux / macOS: `source .env/bin/activate`
4. Instala las dependencias:
   ```bash
   pip install -r requirements.txt
   ```

## Uso

1. Ábrelo en Visual Studio Code (con la extensión Jupyter) o súbelo a Google Colab.
2. Ejecuta todas las celdas en orden: menú **Run → Run All Cells**, o con `Shift + Enter` celda por celda.

3. **Cambiar parámetros:** todos los valores están en la celda **Parámetros globales** (al inicio de cada notebook). Edítala y ejecuta *Run All*; las gráficas de variaciones, los casos y los modelos modificados leen de ahí.
4. **Panel interactivo:** la última sección de `Modelo_temperatura.ipynb` tiene deslizadores (`ipywidgets`) para mover `k`, `Ta`, `T0`, el tiempo y el paso `h` y ver las 6 gráficas actualizarse. Funciona en Jupyter, VS Code y Google Colab.

> **Importante:** la imagen del modelo se carga desde `resources/Modelo-temperatura.png`. Mantén la carpeta `resources/` junto a los notebooks, o la imagen no se mostrará. Los notebooks también guardan ahí sus gráficas.

## Estructura del proyecto

```
.
├── Modelo_temperatura.ipynb          # Clase Escenario, equilibrio, variaciones y modelos modificados
├── Modelo_temperatura_Euler.ipynb    # Método de Euler programado paso a paso con NumPy
├── resources/
│   ├── Modelo-temperatura.png        # Imagen del modelo
│   └── *.png                         # Gráficas exportadas por los notebooks
├── requirements.txt                  # Dependencias del proyecto
├── .gitignore
└── README.md
```

## Qué contiene cada notebook

| Notebook | Contenido |
|---|---|
| `Modelo_temperatura.ipynb` | Celda de parámetros globales, clase `Escenario` (RK45 + Euler + RK4 + solución simbólica), equilibrio y autovalores, 4 casos de estudio (enfriamiento y calentamiento), campo de direcciones, línea de fase, error y estabilidad de Euler, variaciones de `k`, `Ta` y `T0`, mapa de calor, tres modelos modificados (ambiente variable, sistema acoplado y fuente de calor), escala semilog, constante de tiempo τ, Euler vs. RK4, panel completo `graficar_todo()` y panel interactivo con deslizadores |
| `Modelo_temperatura_Euler.ipynb` | Euler con arreglos y ciclos `for`, comparación con la solución exacta, campo de direcciones con flechas coloreadas, línea de fase, variaciones de parámetros, efecto del paso `h` y ambiente variable |

## Gráficas disponibles

| Gráfica | Qué muestra |
|---|---|
| Series de tiempo | T(t) para enfriamiento, calentamiento y equilibrio, con RK45, Euler y RK4 |
| Campo de direcciones | Pendiente dT/dt en el plano (t, T) con trayectorias, también para varios `k` |
| Línea de fase | dT/dt contra T, con el equilibrio marcado |
| Variaciones | Curvas al variar `k`, `Ta` y `T0` por separado |
| Velocidad de cambio | dT/dt en el tiempo |
| Tiempo vs. `k` | Tiempo para llegar a una temperatura objetivo |
| Mapa de calor | T(t) como función de `k` y `T0` con curvas de nivel |
| Escala semilog | ln\|T − T*\| es una recta de pendiente −k (sirve para estimar `k` con datos) |
| Constante de tiempo τ = 1/k | Marcas en τ, 2τ y 3τ sobre la curva |
| Error numérico | Euler (orden 1) vs. RK4 (orden 4) y estabilidad cuando h·k > 2 |
| Fuente de calor | Equilibrio T* = Ta + q/k para distintos `q` |
| Sistema acoplado | Series y retrato de fase 2D objeto–aire–exterior |
| Panel completo | `graficar_todo()` junta 6 de estas gráficas en una sola figura |

## Cómo usar la clase `Escenario`

Cada escenario es un objeto independiente con sus propios parámetros:

```python
# 1. Crear el escenario con los parámetros del modelo
modelo = Escenario(k=0.15, T_amb=20.0)

# 2. Simular con temperatura inicial, tiempo final y paso
modelo.simular(T0=90.0, tiempo_final=30, paso=0.01)

# 3. Calcular equilibrio, Jacobiano y autovalor
resultados = modelo.calcular_puntos_equilibrio()

# 4. Graficar
modelo.graficar_series()   # Temperatura vs. tiempo

# 5. Panel con las 6 gráficas principales (series, campo, línea de fase, velocidad, semilog, error)
modelo.graficar_todo(T0=90.0, tiempo_final=30, h=0.5)
```

### Métodos disponibles

| Método | Qué hace |
|---|---|
| `simular(...)` | Resuelve la ecuación con RK45 y devuelve `(tiempo, temperatura)`. |
| `euler(T0, tiempo_final, h)` | Resuelve con el método de Euler y devuelve `(t, T)`. |
| `rk4(T0, tiempo_final, h)` | Resuelve con Runge-Kutta de orden 4 y devuelve `(t, T)`. |
| `solucion_exacta(T0, t)` | Evalúa `Ta + (T0 - Ta)e^(-kt)`. |
| `solucion_simbolica(T0)` | Resuelve la EDO con SymPy (`dsolve`). |
| `calcular_puntos_equilibrio()` | Devuelve el punto de equilibrio, el Jacobiano, el autovalor y la estabilidad. |
| `tiempo_para_alcanzar(T0, T_objetivo)` | Tiempo que tarda el objeto en llegar a una temperatura. |
| `T_equilibrio()` | Devuelve la temperatura de equilibrio (`Ta`; en el modelo con fuente es `Ta + q/k`). |
| `constante_de_tiempo()` | Devuelve τ = 1/k. |
| `graficar_series()` | Muestra la evolución de la temperatura en el tiempo. |
| `graficar_semilog(T0, tiempo_final)` | Grafica \|T − T*\| en escala logarítmica (una recta). |
| `graficar_todo(T0, tiempo_final, h)` | Panel de 6 gráficas del escenario; `guardar="ruta.png"` lo exporta. |
| `campo_de_direcciones(ax, ...)` | Dibuja el campo de direcciones en el plano (t, T) sobre un eje dado. |
| `linea_de_fase(ax, ...)` | Dibuja `dT/dt` contra `T` con el equilibrio marcado. |

### Comparar varios escenarios en una misma figura

```python
plt.figure(figsize=(10, 5))
for caso in [caso1, caso2, caso3, caso4]:
    plt.plot(caso.tiempo, caso.temperatura, label=f"T0 = {caso.T0}, k = {caso.k}")
plt.axhline(20, color="gray", linestyle="--")
plt.xlabel("Tiempo (min)")
plt.ylabel("Temperatura (°C)")
plt.legend()
plt.grid(True)
plt.show()
```

## Las ecuaciones en el código

El modelo matemático está escrito dos veces dentro de la clase `Escenario`, porque cada versión cumple una función distinta.

**1. Versión simbólica (SymPy), en `__init__`**: se usa para calcular el equilibrio y el Jacobiano.

```python
self.t_symb, self.T_symb = sp.symbols("t T", real=True)
self.ecuacion = -self.k * (self.T_symb - self.T_amb)
```

**2. Versión numérica (SciPy / Euler), en `edo`**: se usa para simular la evolución en el tiempo.

```python
def edo(self, tiempo, T):
    return -self.k * (T - self.T_amb)
```

> **Regla de oro:** si cambias una ecuación, cámbiala en los dos lugares (y en `solucion_exacta` si conoces la solución analítica). Si solo modificas una, la simulación y el análisis de equilibrio describirán modelos distintos.

## Adaptar las ecuaciones a otros modelos

Las ecuaciones son independientes del resto del código: la simulación, Euler, el equilibrio y las gráficas funcionan con cualquier ecuación de una variable.

### Ejemplo 1: Fuente de calor constante (ya incluido en el notebook)

```python
class EscenarioFuente(Escenario):
    def __init__(self, k=0.15, T_amb=20.0, q=3.0):
        super().__init__(k=k, T_amb=T_amb)
        self.q = q
        self.ecuacion = -self.k * (self.T_symb - self.T_amb) + self.q   # simbólica

    def edo(self, tiempo, T):
        return -self.k * (T - self.T_amb) + self.q                      # numérica
```

El equilibrio se desplaza a `T* = Ta + q/k`.

### Ejemplo 2: Ambiente variable (ya incluido en el notebook)

Se reemplaza `T_amb` por una función del tiempo `Ta(t) = Tm + A·sin(2πt/P)`. En este caso ya no hay un equilibrio fijo: el objeto sigue al ambiente con menor amplitud y con retraso.

### Ejemplo 3: Enfriamiento con radiación (no lineal)

Se agrega un término de radiación proporcional a la cuarta potencia de la temperatura absoluta (en kelvin):

```python
# En __init__ (simbólica)
self.ecuacion = -self.k * (self.T_symb - self.T_amb) - self.eps * (self.T_symb**4 - self.T_amb**4)

# En edo (numérica)
return -self.k * (T - self.T_amb) - self.eps * (T**4 - self.T_amb**4)
```

Con ecuaciones más complejas, `sp.solve` puede tardar más o no encontrar todas las soluciones de forma exacta, y la solución analítica deja de existir en forma simple.

### Si tu modelo tiene más de una variable

Mira la clase `EscenarioAcoplado` del notebook: usa un vector de estado `[T, Ta]`, un Jacobiano de 2×2 y un retrato de fase en 2D. Hay que modificar:

- `sp.symbols(...)`: agrega el nuevo símbolo.
- `sistema_edo`: desempaqueta el nuevo valor de estado y devuelve su derivada en la lista.
- `simular`: agrega la condición inicial y extrae la nueva fila de `solucion.y`.
- `calcular_puntos_equilibrio`: incluye la nueva ecuación y el nuevo símbolo en `sp.solve` y en `.jacobian(...)`.

## Qué más puedes cambiar sin tocar las ecuaciones

| Qué | Dónde |
|---|---|
| Valores de los parámetros de todo el notebook | Celda **Parámetros globales** (`k_base`, `Ta_base`, `T0_base`, listas de variaciones, etc.) |
| Un escenario individual | Al crear el escenario: `Escenario(k=..., T_amb=...)` |
| Temperatura inicial, duración y paso | Al llamar a `simular(...)` o `euler(...)` |
| Método de integración | El argumento `method` de `solve_ivp` (por ejemplo `'RK23'`, `'Radau'` o `'LSODA'`) |

## Resultados incluidos

Con `k = 0.15` y `Ta = 20`, el notebook encuentra un único punto de equilibrio:

| Punto T* | Autovalor | Tipo |
|---|---|---|
| 20 | −0.15 | Nodo estable (atractor): toda temperatura inicial tiende a Ta |

Otros resultados:

- El objeto pasa de 90 °C a 40 °C en unos 8.35 min.
- Euler converge con orden 1 (el error baja a la mitad al reducir `h` a la mitad) y diverge si `h·k > 2`.
- Con ambiente variable (`k = 0.5`, `A = 8`, `P = 24 h`) la amplitud del objeto es ≈ 7.09 °C y se retrasa ≈ 1.84 h respecto al ambiente.
- En el sistema acoplado el equilibrio `(Text, Text)` es un nodo estable (autovalores reales negativos, sin oscilaciones).
- Con una fuente de calor `q`, el equilibrio pasa a `Ta + q/k`.
- Constante de tiempo τ = 1/k ≈ 6.67 min: tras 3τ el objeto recorre el 95 % de la distancia al equilibrio.
- RK4 es mucho más preciso que Euler con el mismo paso (con h = 0.5 el error máximo es ≈ 7×10⁻⁶ °C frente a ≈ 1 °C).

## Problemas frecuentes

| Problema | Solución |
|---|---|
| `ModuleNotFoundError: No module named 'sympy'` (u otra librería) | Ejecuta `pip install -r requirements.txt` con el entorno virtual activado. |
| La imagen del modelo no aparece | Verifica que exista `resources/Modelo-temperatura.png` junto al notebook. |
| Las gráficas no se muestran | Ejecuta la celda de imports primero y usa un entorno con Jupyter (no un script sin interfaz gráfica). |
| No aparecen los deslizadores del panel interactivo | Ejecuta `pip install ipywidgets` y reinicia el kernel. En Google Colab ya vienen instalados. |
| Error al guardar una gráfica (`savefig`) | Verifica que la carpeta `resources/` exista junto al notebook. |
| El notebook usa un kernel que no existe en tu equipo | En Jupyter: **Kernel → Change Kernel** y elige tu entorno de Python. |
