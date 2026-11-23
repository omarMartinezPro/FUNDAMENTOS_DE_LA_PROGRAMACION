# SEMANA 7 — ARCHIVOS Y ENTREGA FINAL DEL PROYECTO

**Curso:** Fundamentos de programación
**Semana:** 7 · del lunes 23 al viernes 27 de noviembre de 2026
**Entrega final del proyecto:** viernes 27, 23:59 h (sin prórroga)

> Hasta hoy, todo lo que hacía tu programa se perdía al cerrarlo, como un ensayo que nadie grabó. Esta semana aprendes a **guardar en archivos**, para que la información siga ahí la próxima vez que lo abras. Con eso tu proyecto final deja de ser una demo y se vuelve una herramienta de verdad.

---

## ÍNDICE

1. Tema 17: Escribir archivos
2. Tema 18: Leer archivos
3. Tema 19: Leer, procesar y escribir
4. Tema 20: Errores al trabajar con archivos
5. Las herramientas extra del proyecto (`time` y `os`)
6. Chuleta final

---

# 1. TEMA 17: ESCRIBIR ARCHIVOS

### 1.1 La idea: la libreta del roadie
Lo que hay en tus variables es **memoria de corto plazo**: se borra al cerrar el programa. Un archivo `.txt` es la **libreta del roadie**: lo que anotas ahí sigue escrito mañana.

```
[ variables (se borran) ]  --- write() --->  [ archivo .txt (se queda) ]
```

### 1.2 `open()` y sus tres modos

| Modo | En la libreta | Si el archivo **no existe** | Si **ya existe** |
| :---: | :--- | :--- | :--- |
| `"w"` | Arrancas una **hoja nueva** | Lo crea | ⚠️ **Borra todo** y empieza de cero |
| `"a"` | Anotas **al final** | Lo crea | Conserva lo anterior y agrega abajo |
| `"r"` | Solo **lees** | ❌ `FileNotFoundError` | Lo lee sin cambiarlo |

### 1.3 `with`: la puerta del backstage que se cierra sola
```python
with open("ensayo.txt", "w", encoding="utf-8") as archivo:
    archivo.write("Ensayo del sábado\n")
    archivo.write("Afinar guitarras\n")
# aquí el archivo ya se cerró solo
```
- `with` abre el archivo y lo **cierra automáticamente** al terminar el bloque, aunque haya un error. En este curso **siempre** usamos `with`.
- `write()` **no** agrega el salto de línea: ponle `\n` al final de cada renglón.
- `encoding="utf-8"` evita que los **acentos y la ñ** salgan como `Ã±` al abrir el archivo en otra computadora (sobre todo en Windows). Ponlo siempre.

### 1.4 `"w"` contra `"a"`
```python
with open("setlist.txt", "w", encoding="utf-8") as f:
    f.write("Intro\n")
with open("setlist.txt", "w", encoding="utf-8") as f:   # hoja nueva: borra "Intro"
    f.write("Riff\n")
with open("setlist.txt", "a", encoding="utf-8") as f:   # anota al final
    f.write("Solo\n")
# setlist.txt queda:  Riff / Solo
```

---

# 2. TEMA 18: LEER ARCHIVOS

| Forma | Qué te da | Cuándo usarla |
| :--- | :--- | :--- |
| `archivo.read()` | **Todo** el texto en un solo string | Mostrar el archivo completo |
| `archivo.readline()` | **Una** línea (cada llamada avanza) | Leer solo el encabezado |
| `archivo.readlines()` | Una **lista** con todas las líneas | Contar líneas o usarlas por índice |
| `for linea in archivo:` | Línea por línea | La forma más común para procesar |

```python
numero = 0
with open("canciones.txt", "r", encoding="utf-8") as archivo:
    for linea in archivo:
        numero = numero + 1
        print(f"{numero}. {linea.strip()}")   # strip() quita el \n del final
```

> 🔑 **Todo lo que lees de un archivo es texto**, igual que con `input()`. Si el archivo trae números, conviértelos con `int()` o `float()`.

---

# 3. TEMA 19: LEER, PROCESAR Y ESCRIBIR

Es el modelo Entrada → Proceso → Salida de la semana 1, pero con archivos:

```
[ archivo de entrada ] → leer ("r") → procesar en variables → escribir ("w" o "a") → [ archivo de salida ]
```

### 3.1 Renglones con varios datos: `split(",")`
```python
# ventas.txt:   A,3 / C,10 / B,4 / A,2 / C,6
totales = {}
with open("ventas.txt", "r", encoding="utf-8") as f:
    for linea in f:
        zona, cant = linea.strip().split(",")   # "A,3" → ["A", "3"]
        if zona in totales:
            totales[zona] = totales[zona] + int(cant)
        else:
            totales[zona] = int(cant)
print(totales)   # {'A': 5, 'C': 16, 'B': 4}
```

### 3.2 Registrar con la fecha en una tupla (pieza del proyecto)
```python
def anexar(nombre_archivo, texto, fecha):
    dia, mes, anio = fecha                      # desempaquetar la tupla
    with open(nombre_archivo, "a", encoding="utf-8") as f:
        f.write(f"[{dia}/{mes}/{anio}] {texto}\n")

Fecha = 23, 11, 2026                            # tupla sin paréntesis
anexar("bitacora.txt", "Cambio de aceite KLR", Fecha)
# bitacora.txt:  [23/11/2026] Cambio de aceite KLR
```

---

# 4. TEMA 20: ERRORES AL TRABAJAR CON ARCHIVOS

El casco de la semana 5, ahora para archivos:

| Excepción | Cuándo pasa | En el concierto |
| :--- | :--- | :--- |
| `FileNotFoundError` | Abres con `"r"` un archivo que no existe, o escribes en una carpeta que no existe | Pides el case de la guitarra y no está en la bodega |
| `PermissionError` | El sistema no te deja abrirlo o escribirlo | Quieres entrar al camerino sin pase |
| `IsADirectoryError` | Escribiste el nombre de una **carpeta** en lugar de un archivo (Mac y Linux; en Windows sale `PermissionError`) | Pediste "la bodega" en vez de "el case" |
| `ValueError` | Un renglón del archivo no se puede convertir con `int()`, o `split` no da los datos esperados | La etiqueta del case está borrada |

```python
nombre = input("Archivo a abrir: ")
try:
    with open(nombre, "r", encoding="utf-8") as f:
        print(f.read())
except FileNotFoundError:
    print(f"'{nombre}' no existe. Revisa cómo lo escribiste.")
except PermissionError:
    print(f"No tengo permiso para abrir '{nombre}'.")
except IsADirectoryError:
    print(f"'{nombre}' es una carpeta, no un archivo.")
```

### 4.1 Renglones malos sin tronar
```python
with open("inventario.txt", "r", encoding="utf-8") as f:
    for linea in f:
        try:
            producto, cant = linea.strip().split(",")
            stock[producto] = int(cant)
        except ValueError:          # "gorra,x" o "sudadera" (sin coma)
            print(f"Renglón con error: '{linea.strip()}'")
```

---

# 5. LAS HERRAMIENTAS EXTRA DEL PROYECTO

### 5.1 `time.sleep()`: la pausa
```python
import time

def pantalla_carga(segundos):
    pasos = 10
    for i in range(1, pasos + 1):
        barra = "#" * i + "." * (pasos - i)
        print(f"Cargando [{barra}] {i * 10}%")
        time.sleep(segundos / pasos)       # espera un poquito en cada paso
    print("¡Sistema listo!")

pantalla_carga(3)   # 3 segundos en total (el proyecto pide máximo 5)
```

### 5.2 La inactividad simulada
Cuando el usuario presiona **Enter sin elegir opción**, el programa entiende que "se alejó del teclado" y simula 10 minutos con un `for`: **cada vuelta representa un minuto**.
```python
def inactividad():
    for minuto in range(1, 11):
        print(f"...{minuto} min sin actividad")
        time.sleep(0.2)
    while True:
        resp = input("¿Deseas continuar? (si/no): ").strip().lower()
        if resp == "si":
            return True        # sigue en el menú
        elif resp == "no":
            return False       # regresa a la pantalla de inicio
        print("Escribe exactamente si o no")
```
> ¿Por qué simulada? `input()` detiene el programa mientras espera, así que ningún ciclo puede contar el tiempo **mientras** el usuario no escribe. Medirlo de verdad requiere herramientas que no son de este curso.

### 5.3 `os.listdir()`: ¿qué archivos hay en la carpeta?
```python
import os

def archivos_disponibles():
    lista = []
    for nombre in os.listdir("."):        # "." = la carpeta donde corre tu programa
        if nombre.endswith(".txt"):
            lista.append(nombre)
    lista.sort()
    return lista
```
> ⚠️ Ejecuta tu programa **desde la carpeta del proyecto**. En VS Code, abre la carpeta `semana7/proyecto/` (Archivo → Abrir carpeta) antes de darle ▶. Si no, `os.listdir(".")` mira otra carpeta y "no encuentra" tus archivos.

### 5.4 El menú como matriz
```python
MENU = [
    ["1", "Ver archivos"],
    ["2", "Leer archivo"],
    ["3", "Crear archivo"],
    ["4", "Agregar registro"],
    ["5", "Salir"],
]
for fila in MENU:
    print(f"| {fila[0]} | {fila[1]:<18} |")   # :<18 rellena con espacios hasta 18 caracteres
```

---

# 6. CHULETA FINAL

```python
import os, time

# ESCRIBIR
with open("x.txt", "w", encoding="utf-8") as f:   # hoja nueva (borra)
    f.write("renglón\n")
with open("x.txt", "a", encoding="utf-8") as f:   # al final (conserva)
    f.write("otro renglón\n")

# LEER
with open("x.txt", "r", encoding="utf-8") as f:
    texto = f.read()            # todo
with open("x.txt", "r", encoding="utf-8") as f:
    for linea in f:             # renglón por renglón
        datos = linea.strip().split(",")

# ERRORES
try:
    ...
except FileNotFoundError: ...
except PermissionError: ...

# PROYECTO
Fecha = dia, mes, anio         # tupla
time.sleep(0.5)                # pausa
os.listdir(".")                # archivos de la carpeta
```
