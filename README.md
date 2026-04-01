# UPUNTES UNIDAD 2: 


## Definición de componentes, paquetes / librerías

Las librerías en Python son conjuntos de módulos y funciones previamente desarrollados que permiten realizar tareas específicas sin necesidad de programarlas desde cero. Estas facilitan el desarrollo de aplicaciones, ya que ofrecen soluciones listas para usar en diferentes áreas como interfaces gráficas, análisis de datos, inteligencia artificial y automatización.

El uso de librerías permite optimizar el tiempo de desarrollo, mejorar la organización del código y aumentar la eficiencia del programa.

```python
import flet as ft
import matplotlib.pyplot as plt
import flet_charts as fch
import random
```
En este fragmento se importan varias librerías. Por ejemplo, flet se utiliza para crear la interfaz gráfica, matplotlib.pyplot permite generar gráficas, flet_charts sirve para mostrar esas gráficas dentro de la aplicación, y random se usa para generar datos aleatorios.


Los componentes son elementos visuales o funcionales que forman parte de una interfaz de usuario. En el caso de aplicaciones desarrolladas con librerías como Flet, los componentes pueden ser botones, textos, contenedores, imágenes, entre otros, los cuales permiten construir la estructura y el diseño de la aplicación.

Además, los componentes pueden ser reutilizables y personalizados, lo que facilita la creación de interfaces organizadas y dinámicas.


```python
class ProductCard(ft.Container):

```
En este caso, se está creando un componente personalizado llamado ProductCard, que hereda de ft.Container.

Dentro de este componente se integran otros elementos como:

```python
ft.Text(producto["nombre"]),
ft.Image(src=f"assets/{producto['ruta_imagen']}"),
ft.ElevatedButton("Agregar al carrito")
```
Este ejemplo evidencia cómo un componente puede integrar y organizar diversos elementos visuales dentro de una misma estructura, con el propósito de representar un producto en una tienda. En él se incluyen datos relevantes como el nombre, la imagen y el precio, además de un botón que permite realizar acciones, lo que contribuye a una interfaz más interactiva y funcional.

**Codigo completo**
```python
import flet as ft


class ProductCard(ft.Container):

    def __init__(self, producto, agregar_carrito):
        super().__init__()

        self.producto = producto
        self.favorito = False

        self.width = 260
        self.padding = 15
        self.margin = 10
        self.border_radius = 15
        self.bgcolor = ft.Colors.WHITE

        self.shadow = ft.BoxShadow(
            blur_radius=15,
            color=ft.Colors.BLACK12,
            offset=ft.Offset(2, 2)
        )

        def toggle_favorito(e):
            self.favorito = not self.favorito

            if self.favorito:
                e.control.icon = ft.Icons.FAVORITE
                e.control.icon_color = ft.Colors.RED
            else:
                e.control.icon = ft.Icons.FAVORITE_BORDER
                e.control.icon_color = None

            e.control.update()

        corazon = ft.IconButton(
            icon=ft.Icons.FAVORITE_BORDER,
            on_click=toggle_favorito
        )

        self.content = ft.Column(
            spacing=10,
            controls=[

                ft.Text(
                    f"ID: {producto['id']}",
                    size=12,
                    weight=ft.FontWeight.BOLD,
                    color=ft.Colors.BLACK
                ),

                ft.Image(
                    src=f"assets/{producto['ruta_imagen']}",
                    width=230,
                    height=150
                ),

                ft.Text(
                    producto["nombre"],
                    size=18,
                    weight=ft.FontWeight.BOLD,
                    color=ft.Colors.BLACK
                ),

                ft.Text(
                    producto["descripcion"],
                    size=12,
                    weight=ft.FontWeight.BOLD,
                    color=ft.Colors.BLACK
                ),

                ft.Text(
                    f"${producto['precio']:,}",
                    size=18,
                    weight=ft.FontWeight.BOLD,
                    color=ft.Colors.BLACK
                ),

                ft.Row(
                    alignment=ft.MainAxisAlignment.SPACE_BETWEEN,
                    controls=[
                        corazon,

                        ft.ElevatedButton(
                            "Agregar al carrito",
                            icon=ft.Icons.SHOPPING_CART,
                            on_click=lambda e: agregar_carrito(self.producto)
                        )
                    ]
                )
            ]
        )


def main(page: ft.Page):

    page.title = "Tienda de Tecnología"
    page.bgcolor = "#CFEFFF"
    page.scroll = "auto"

    carrito = {}
    contador = ft.Text(
        "0",
        size=20,
        weight=ft.FontWeight.BOLD,
        color=ft.Colors.BLACK
    )

    texto_carrito = ft.Text(
        "Carrito vacío",
        size=16,
        weight=ft.FontWeight.BOLD,
        color=ft.Colors.BLACK
    )


    def agregar_carrito(producto):

        nombre = producto["nombre"]

        if nombre in carrito:
            carrito[nombre] += 1
        else:
            carrito[nombre] = 1

        contador.value = str(sum(carrito.values()))
        page.update()


    def mostrar_carrito(e):

        lista = ""

        for nombre, cantidad in carrito.items():
            lista += f"{nombre} x{cantidad}\n"

        if lista == "":
            lista = "El carrito está vacío"

        texto_carrito.value = lista
        page.update()


    productos = [

        {
            "id": 1,
            "nombre": "Computadora",
            "descripcion": "Computadora de alto rendimiento",
            "precio": 20000,
            "ruta_imagen": "computadora.jpg"
        },

        {
            "id": 2,
            "nombre": "Bocina Bluetooth",
            "descripcion": "Bocina portátil",
            "precio": 900,
            "ruta_imagen": "bocina.jpg"
        },

        {
            "id": 3,
            "nombre": "Televisión",
            "descripcion": "Smart TV 4K",
            "precio": 7500,
            "ruta_imagen": "television.jpg"
        },

        {
            "id": 4,
            "nombre": "Audífonos",
            "descripcion": "Audio envolvente",
            "precio": 1200,
            "ruta_imagen": "audifonos.jpg"
        },

        {
            "id": 5,
            "nombre": "Teclado Mecánico",
            "descripcion": "Teclado RGB",
            "precio": 1500,
            "ruta_imagen": "teclado.jpg"
        }

    ]


    grid = ft.Row(
        wrap=True,
        spacing=20,
        run_spacing=20,
        alignment=ft.MainAxisAlignment.CENTER
    )


    def mostrar_productos(lista):

        grid.controls.clear()

        for producto in lista:
            grid.controls.append(ProductCard(producto, agregar_carrito))

        page.update()


    def filtrar_productos(texto):

        filtrados = []

        for p in productos:
            if texto.lower() in p["nombre"].lower():
                filtrados.append(p)

        mostrar_productos(filtrados)


    # BUSCADOR CORREGIDO
    buscador = ft.TextField(
        hint_text="Buscar producto...",
        width=300,
        bgcolor=ft.Colors.WHITE,
        text_style=ft.TextStyle(
            color=ft.Colors.BLACK,
            weight=ft.FontWeight.BOLD
        ),
        hint_style=ft.TextStyle(
            color=ft.Colors.BLACK,
            weight=ft.FontWeight.BOLD
        ),
        on_change=lambda e: filtrar_productos(e.control.value)
    )


    mostrar_productos(productos)


    header = ft.Row(
        alignment=ft.MainAxisAlignment.SPACE_BETWEEN,
        controls=[

            ft.Text(
                "TIENDA DE TECNOLOGÍA",
                size=30,
                weight=ft.FontWeight.BOLD,
                color=ft.Colors.BLACK
            ),

            ft.Row(
                controls=[
                    ft.IconButton(
                        icon=ft.Icons.SHOPPING_CART,
                        on_click=mostrar_carrito
                    ),
                    contador
                ]
            )
        ]
    )


    page.add(
        header,
        buscador,
        grid,
        ft.Divider(),
        ft.Text(
            "PRODUCTOS EN CARRITO",
            size=20,
            weight=ft.FontWeight.BOLD,
            color=ft.Colors.BLACK
        ),
        texto_carrito
    )


ft.run(main, assets_dir="assets")
```
## Uso de librerías proporcionadas por el lenguaje.
En los códigos desarrollados en clase se utilizaron tanto librerías propias del lenguaje Python como librerías externas, por un lado, librerías como dataclasses y random forman parte de la biblioteca estándar de Python, por lo que no requieren instalación adicional.
Por otro lado, librerías como matplotlib, flet y flet_charts son externas, ya que deben ser instaladas previamente y amplían las funcionalidades del lenguaje, permitiendo la creación de interfaces gráficas y visualización de datos.
Librería: dataclasses

La librería dataclasses es un módulo incluido en la biblioteca estándar de Python que permite crear clases diseñadas principalmente para almacenar datos de forma sencilla y estructurada. Su principal ventaja es que reduce la cantidad de código necesario, ya que genera automáticamente métodos especiales como el constructor (_init), el método de representación (repr_) y otros, sin que el programador tenga que implementarlos manualmente.

Esta librería resulta especialmente útil cuando se trabaja con objetos que únicamente contienen atributos, como registros, configuraciones o entidades dentro de un sistema. Además, mejora la legibilidad del código y facilita su mantenimiento, ya que permite definir clases de manera más clara, ordenada y concisa.

El módulo dataclasses, introducido en Python 3.7, proporciona un decorador llamado @dataclass, el cual genera automáticamente diversos métodos especiales dentro de una clase. Entre ellos se encuentran el método _init, que permite inicializar los atributos; el método __repr, que ofrece una representación en forma de cadena del objeto; y el método __eq, que facilita la comparación entre instancias. Asimismo, puede generar métodos de ordenación como __lt, __le, __gt_ y _ge_ cuando se requiere.

Gracias a estas características, dataclasses permite definir clases de una forma más limpia, directa y eficiente, enfocándose únicamente en la definición de los datos.

**Ejemplo**
En este código se utiliza la librería dataclasses para definir la clase Usuario, la cual está diseñada únicamente para almacenar información.
```python
import flet as ft
from dataclasses import dataclass

# Clase de solo datos
@dataclass
class Usuario:
    nombre: str
    rol: str
    color_borde: ft.Colors = ft.Colors.BLUE
```
En este fragmento se emplea el decorador @dataclass, lo que indica que la clase no necesita métodos definidos manualmente, ya que Python genera automáticamente el constructor (_init_) y otros métodos necesarios.

La clase Usuario contiene tres atributos: nombre, rol y color_borde, los cuales representan la información de cada usuario. Gracias al uso de dataclasses, es posible crear objetos de esta clase de forma directa, como se muestra a continuación:
```python
    # Creamos objetos Usuario
    usuario1 = Usuario("Ana Garcia", "Desarrolladora Senior", ft.Colors.GREEN)
```
En este caso, no fue necesario definir un constructor explícito, ya que dataclasses lo genera automáticamente. Esto simplifica el código y permite enfocarse únicamente en los datos que se desean manejar.

**Codigo**
```python

import flet as ft
from dataclasses import dataclass

# Clase de solo datos
@dataclass
class Usuario:
    nombre: str
    rol: str
    color_borde: ft.Colors = ft.Colors.BLUE


# Definicion del componente personalizado
class TarjetaPerfil(ft.Container):

    def __init__(self, usuario: Usuario):
        super().__init__()

        self.usuario = usuario  # Guardamos el objeto completo

        self.content = ft.Column(
            controls=[
                ft.Text(usuario.nombre, weight=ft.FontWeight.BOLD, size=20),
                ft.Text(usuario.rol, italic=True),
                ft.ElevatedButton("Ver Perfil", on_click=self.saludar)
            ],
            tight=True
        )

        self.border = ft.border.all(2, usuario.color_borde)
        self.padding = 10
        self.border_radius = 10
        self.width = 200

    def saludar(self, e):
        print(f"Interactuando con el componente de {self.usuario.nombre}")


def main(page: ft.Page):
    page.title = "Unidad 2 : Componentes Definidos por el Usuario"
    page.horizontal_alignment = ft.CrossAxisAlignment.CENTER

    # Creamos objetos Usuario
    usuario1 = Usuario("Ana Garcia", "Desarrolladora Senior", ft.Colors.GREEN)
    usuario2 = Usuario("Carlos Ruiz", "Arquitecto de Software")

    # Pasamos el objeto completo al componente
    tarjeta1 = TarjetaPerfil(usuario1)
    tarjeta2 = TarjetaPerfil(usuario2)

    page.add(
        ft.Text("Lista de Usuarios", size=30, weight=ft.FontWeight.BOLD),
        ft.Row(
            [tarjeta1, tarjeta2], alignment=ft.MainAxisAlignment.CENTER
        )
    )

ft.app(target=main)

```
**Libreria random**
La librería random es un módulo incluido en la biblioteca estándar de Python que permite generar números pseudoaleatorios y realizar operaciones relacionadas con la aleatoriedad. Aunque los valores generados no son completamente aleatorios, son suficientemente precisos para la mayoría de aplicaciones como simulaciones, juegos, pruebas y modelado de datos.

Para utilizar esta librería, es necesario importarla mediante:
```python

import random
```
**Funciones principales de random**

Entre las funciones más importantes se encuentran:
-random.random() → Genera un número decimal aleatorio en el rango [0, 1).

random.randint(a, b) → Genera un número entero aleatorio entre a y b (incluidos).

-random.uniform(a, b) → Genera un número decimal aleatorio entre a y b.

-random.choice(seq) → Selecciona un elemento aleatorio de una secuencia.

-random.sample(seq, k) → Devuelve k elementos aleatorios sin repetición.

-random.shuffle(seq) → Reordena los elementos de una lista de forma aleatoria.

-random.seed(n) → Inicializa el generador para obtener resultados reproducibles.

-random.gauss(mu, sigma) → Genera números con distribución normal (gaussiana).

En el siguiente programa  la librería random se utiliza específicamente en la función encargada de crear la gráfica de dispersión.
```python
import flet as ft
import matplotlib.pyplot as plt
import flet_charts as fch
import random
```

En esta línea se utiliza la función random.randint(10, 50), la cual genera números enteros aleatorios entre 10 y 50.

Estos valores se crean dentro de una lista mediante un ciclo que se repite 20 veces, lo que permite generar automáticamente un conjunto de datos.
```python
    y = [random.randint(10, 50) for _ in range(20)]
```
Gracias a random, se obtiene un conjunto de datos diferente cada vez que se ejecuta el programa. Estos datos se utilizan en la instrucción: 

```python
    ax.scatter(x, y)
```
Lo que permite crear una gráfica de dispersión con valores dinámicos, esto hace que la gráfica sea más realista, ya que no utiliza datos fijos, sino que muestra variaciones como ocurriría en situaciones reales.
```python
**Codigo**

import flet as ft
import matplotlib.pyplot as plt
import flet_charts as fch
import random


# Gráfica de barras
def crear_barras():
    nombres = ["A", "B", "C", "D"]
    ventas = [15, 30, 45, 10]

    fig, ax = plt.subplots()
    ax.bar(nombres, ventas) # crea las graficas
    ax.set_title("Ventas por producto")
    ax.set_xlabel("Productos")
    ax.set_ylabel("Ventas")

    return fig


#  Gráfica circular
def crear_circular():
    nombres = ["Ana", "Carlos", "Luis", "Sofia"]
    ventas = [10, 15, 7, 12]

    fig, ax = plt.subplots()
    ax.pie(ventas, labels=nombres, autopct="%1.1f%%") #crea las graficas
    ax.set_title("Distribución de ventas")

    return fig


# Gráfica de líneas
def crear_lineas():
    meses = ["Ene", "Feb", "Mar", "Abr", "May"]
    rendimiento = [10, 25, 18, 40, 35]

    fig, ax = plt.subplots()
    ax.plot(meses, rendimiento, marker="o") #crea la grafica
    ax.set_title("Tendencia de rendimiento")
    ax.set_xlabel("Meses")
    ax.set_ylabel("Rendimiento")
    ax.grid(True)

    return fig


# Gráfica de dispersión
def crear_dispersion():
    x = list(range(20))
    y = [random.randint(10, 50) for _ in range(20)]

    fig, ax = plt.subplots()
    ax.scatter(x, y) #crea las graficas
    ax.set_title("Muestreo de  sensores")
    ax.set_xlabel("Tiempo")
    ax.set_ylabel("Valor")

    return fig


def main(page: ft.Page):

    page.title = "Gráficas con Flet"
    page.horizontal_alignment = ft.CrossAxisAlignment.CENTER

    grafica = ft.Container(width=400, height=300) #el espacio de grafica

    # mostrar barras
    def mostrar_barras(e):
        fig = crear_barras()
        grafica.content = fch.MatplotlibChart(figure=fig)
        page.update()
        plt.close(fig)

    # mostrar circular
    def mostrar_circular(e):
        fig = crear_circular()
        grafica.content = fch.MatplotlibChart(figure=fig)
        page.update()
        plt.close(fig)

    # mostrar líneas
    def mostrar_lineas(e):
        fig = crear_lineas()
        grafica.content = fch.MatplotlibChart(figure=fig)
        page.update()
        plt.close(fig)

    # mostrar dispersión
    def mostrar_dispersion(e):
        fig = crear_dispersion()
        grafica.content = fch.MatplotlibChart(figure=fig)
        page.update()
        plt.close(fig)

    page.add(
        ft.Text("Selecciona el tipo de gráfica", size=25, weight="bold"), 

        ft.Row(
            [
                ft.ElevatedButton("Barras", on_click=mostrar_barras),
                ft.ElevatedButton("Circular", on_click=mostrar_circular),
                ft.ElevatedButton("Líneas", on_click=mostrar_lineas),
                ft.ElevatedButton("Dispersión", on_click=mostrar_dispersion),
            ],
            alignment=ft.MainAxisAlignment.CENTER,
        ),

        grafica
    )


ft.app(target=main)
```
**Libreria matplotlib**
Matplotlib es una librería de Python de código abierto utilizada para la creación de gráficas y la visualización de datos de forma clara y concisa. Es considerada una de las herramientas más populares dentro del lenguaje, ya que permite representar información mediante distintos tipos de gráficos, como gráficas de barras, líneas, dispersión, circulares e histogramas.

Esta librería es ampliamente utilizada debido a su facilidad de uso y a la gran cantidad de opciones de personalización que ofrece, lo que permite adaptar los gráficos a las necesidades específicas de cada usuario. Gracias a esto, resulta una herramienta fundamental para el análisis de datos y la presentación de información visual.

Matplotlib fue desarrollada inicialmente en 2002 por John Hunter con el objetivo de visualizar señales eléctricas del cerebro, buscando replicar en Python las capacidades gráficas de MATLAB. Con el tiempo, ha sido mejorada por la comunidad de código abierto, convirtiéndose en una de las librerías más importantes para la visualización de datos.

Para crear gráficos con Matplotlib, comúnmente se utiliza el módulo pyplot, el cual proporciona funciones sencillas para agregar elementos como líneas, textos e imágenes. El proceso general consiste en crear una figura y sus ejes mediante la función subplots(), representar los datos con funciones específicas según el tipo de gráfica, y posteriormente personalizarla agregando títulos, etiquetas o rejillas.


En este programa, la librería matplotlib se utiliza a través de su módulo pyplot, el cual se importa como plt. Esta librería se emplea para crear y visualizar diferentes tipos de gráficas a partir de datos definidos dentro del código.

La librería se utiliza siguiendo una estructura clara dentro de cada función de gráfica (crear_barras, crear_circular, crear_lineas, crear_dispersion):
```python
    fig, ax = plt.subplots()
```

    Primero, se crea una figura (fig) y un conjunto de ejes (ax) usando plt.subplots(). Esta es la base donde se dibuja la gráfica.

Después, se utiliza una función específica dependiendo del tipo de gráfica:
-ax.bar() → gráfica de barras

-ax.pie() → gráfica circular

-ax.plot() → gráfica de líneas

-ax.scatter() → gráfica de dispersión

por ejemplo 
```python

    ax.bar(nombres, ventas)
```
Finalmente, se personaliza la gráfica con funciones como:

```python
  
    ax.set_title("Ventas por producto")
    ax.set_xlabel("Productos")
    ax.set_ylabel("Ventas")
```

Al utilizar matplotlib en este programa, se logra generar diferentes tipos de gráficas (barras, circular, líneas y dispersión) de forma dinámica, cada función crea una gráfica distinta y la devuelve como una figura (fig), la cual posteriormente se muestra en la interfaz gráfica.
```python
import flet as ft
import matplotlib.pyplot as plt
import flet_charts as fch
import random


# Gráfica de barras
def crear_barras():
    nombres = ["A", "B", "C", "D"]
    ventas = [15, 30, 45, 10]

    fig, ax = plt.subplots()
    ax.bar(nombres, ventas) # crea las graficas
    ax.set_title("Ventas por producto")
    ax.set_xlabel("Productos")
    ax.set_ylabel("Ventas")

    return fig


#  Gráfica circular
def crear_circular():
    nombres = ["Ana", "Carlos", "Luis", "Sofia"]
    ventas = [10, 15, 7, 12]

    fig, ax = plt.subplots()
    ax.pie(ventas, labels=nombres, autopct="%1.1f%%") #crea las graficas
    ax.set_title("Distribución de ventas")

    return fig


# Gráfica de líneas
def crear_lineas():
    meses = ["Ene", "Feb", "Mar", "Abr", "May"]
    rendimiento = [10, 25, 18, 40, 35]

    fig, ax = plt.subplots()
    ax.plot(meses, rendimiento, marker="o") #crea la grafica
    ax.set_title("Tendencia de rendimiento")
    ax.set_xlabel("Meses")
    ax.set_ylabel("Rendimiento")
    ax.grid(True)

    return fig


# Gráfica de dispersión
def crear_dispersion():
    x = list(range(20))
    y = [random.randint(10, 50) for _ in range(20)]

    fig, ax = plt.subplots()
    ax.scatter(x, y) #crea las graficas
    ax.set_title("Muestreo de  sensores")
    ax.set_xlabel("Tiempo")
    ax.set_ylabel("Valor")

    return fig


def main(page: ft.Page):

    page.title = "Gráficas con Flet"
    page.horizontal_alignment = ft.CrossAxisAlignment.CENTER

    grafica = ft.Container(width=400, height=300) #el espacio de grafica

    # mostrar barras
    def mostrar_barras(e):
        fig = crear_barras()
        grafica.content = fch.MatplotlibChart(figure=fig)
        page.update()
        plt.close(fig)

    # mostrar circular
    def mostrar_circular(e):
        fig = crear_circular()
        grafica.content = fch.MatplotlibChart(figure=fig)
        page.update()
        plt.close(fig)

    # mostrar líneas
    def mostrar_lineas(e):
        fig = crear_lineas()
        grafica.content = fch.MatplotlibChart(figure=fig)
        page.update()
        plt.close(fig)

    # mostrar dispersión
    def mostrar_dispersion(e):
        fig = crear_dispersion()
        grafica.content = fch.MatplotlibChart(figure=fig)
        page.update()
        plt.close(fig)

    page.add(
        ft.Text("Selecciona el tipo de gráfica", size=25, weight="bold"), 

        ft.Row(
            [
                ft.ElevatedButton("Barras", on_click=mostrar_barras),
                ft.ElevatedButton("Circular", on_click=mostrar_circular),
                ft.ElevatedButton("Líneas", on_click=mostrar_lineas),
                ft.ElevatedButton("Dispersión", on_click=mostrar_dispersion),
            ],
            alignment=ft.MainAxisAlignment.CENTER,
        ),

        grafica
    )


ft.app(target=main)
```
Para que las gráficas se muestren correctamente, se requieren tres elementos clave:

1.	Los datos: Se definen dentro de cada función (por ejemplo, listas como nombres y ventas).

2.	La creación de la gráfica con matplotlib: Usando plt.subplots() y funciones como bar(), pie(), plot() o scatter().
	
3.	La integración con la interfaz (Flet): La gráfica generada se asigna a un componente visual:

```python

        grafica.content = fch.MatplotlibChart(figure=fig)
```
Esto permite mostrar la gráfica dentro de la aplicación.

**Libreria flet_charts**
La librería flet_charts es una extensión de la librería flet que permite integrar gráficos dentro de aplicaciones desarrolladas con interfaces gráficas. Su principal función es servir como puente entre herramientas de visualización de datos, como matplotlib, y los componentes visuales de Flet.

Esta librería facilita la incorporación de gráficas dentro de una interfaz interactiva, permitiendo mostrar información visual directamente en la aplicación sin necesidad de abrir ventanas externas. De esta manera, mejora la experiencia del usuario y hace que las aplicaciones sean más dinámicas y completas.

flet_charts se utiliza para mostrar gráficas dentro de una aplicación creada con Flet. Es especialmente útil cuando se trabaja con librerías como matplotlib, ya que permite integrar las figuras generadas directamente en la interfaz gráfica.

Sin esta librería, las gráficas creadas con matplotlib no podrían visualizarse fácilmente dentro de la aplicación, ya que normalmente se mostrarían en ventanas externas.

El funcionamiento de flet_charts se basa en recibir una figura previamente creada (por ejemplo, con matplotlib) y convertirla en un componente visual compatible con Flet. Esto se logra mediante el uso de un componente llamado MatplotlibChart, el cual permite renderizar la gráfica dentro de un contenedor de la interfaz.


En este programa, la librería flet_charts desempeña un papel fundamental al permitir la integración de gráficas dentro de la interfaz gráfica desarrollada con Flet. Esta librería actúa como un intermediario entre matplotlib, que es la encargada de generar las gráficas, y Flet, que se encarga de mostrarlas en pantalla. Gracias a esta integración, es posible visualizar datos de manera interactiva dentro de la aplicación, sin necesidad de abrir ventanas externas.

Para utilizar esta librería, primero se realiza su importación en el código:
```python
import flet_charts as fch
```
Posteriormente, su uso se observa en las funciones encargadas de mostrar las distintas gráficas. En cada una de ellas, después de generar la figura con matplotlib, se utiliza el componente MatplotlibChart de flet_charts para insertar la gráfica dentro de un contenedor de la interfaz:
```python
        grafica.content = fch.MatplotlibChart(figure=fig)
```
En esta instrucción, la variable fig representa la figura creada previamente mediante matplotlib, la cual contiene la gráfica correspondiente (ya sea de barras, circular, de líneas o de dispersión). El componente MatplotlibChart toma esta figura y la convierte en un elemento visual compatible con Flet, permitiendo que sea mostrada dentro del contenedor grafica.

De esta manera, cada vez que el usuario presiona uno de los botones (por ejemplo, “Barras”, “Circular”, “Líneas” o “Dispersión”), se ejecuta una función que genera la gráfica correspondiente y la asigna al contenedor mediante flet_charts. Esto permite actualizar dinámicamente el contenido visual de la aplicación.

El uso de esta librería tiene como objetivo principal integrar la visualización de datos dentro de la interfaz gráfica, haciendo que la aplicación sea más interactiva y funcional. Gracias a flet_charts, las gráficas no se muestran en ventanas externas, sino directamente en el mismo entorno de la aplicación, lo que mejora la experiencia del usuario y facilita la interacción con la información.

**Codigo**
```python
import flet as ft
import matplotlib.pyplot as plt
import flet_charts as fch
import random


# Gráfica de barras
def crear_barras():
    nombres = ["A", "B", "C", "D"]
    ventas = [15, 30, 45, 10]

    fig, ax = plt.subplots()
    ax.bar(nombres, ventas) # crea las graficas
    ax.set_title("Ventas por producto")
    ax.set_xlabel("Productos")
    ax.set_ylabel("Ventas")

    return fig


#  Gráfica circular
def crear_circular():
    nombres = ["Ana", "Carlos", "Luis", "Sofia"]
    ventas = [10, 15, 7, 12]

    fig, ax = plt.subplots()
    ax.pie(ventas, labels=nombres, autopct="%1.1f%%") #crea las graficas
    ax.set_title("Distribución de ventas")

    return fig


# Gráfica de líneas
def crear_lineas():
    meses = ["Ene", "Feb", "Mar", "Abr", "May"]
    rendimiento = [10, 25, 18, 40, 35]

    fig, ax = plt.subplots()
    ax.plot(meses, rendimiento, marker="o") #crea la grafica
    ax.set_title("Tendencia de rendimiento")
    ax.set_xlabel("Meses")
    ax.set_ylabel("Rendimiento")
    ax.grid(True)

    return fig


# Gráfica de dispersión
def crear_dispersion():
    x = list(range(20))
    y = [random.randint(10, 50) for _ in range(20)]

    fig, ax = plt.subplots()
    ax.scatter(x, y) #crea las graficas
    ax.set_title("Muestreo de  sensores")
    ax.set_xlabel("Tiempo")
    ax.set_ylabel("Valor")

    return fig


def main(page: ft.Page):

    page.title = "Gráficas con Flet"
    page.horizontal_alignment = ft.CrossAxisAlignment.CENTER

    grafica = ft.Container(width=400, height=300) #el espacio de grafica

    # mostrar barras
    def mostrar_barras(e):
        fig = crear_barras()
        grafica.content = fch.MatplotlibChart(figure=fig)
        page.update()
        plt.close(fig)

    # mostrar circular
    def mostrar_circular(e):
        fig = crear_circular()
        grafica.content = fch.MatplotlibChart(figure=fig)
        page.update()
        plt.close(fig)

    # mostrar líneas
    def mostrar_lineas(e):
        fig = crear_lineas()
        grafica.content = fch.MatplotlibChart(figure=fig)
        page.update()
        plt.close(fig)

    # mostrar dispersión
    def mostrar_dispersion(e):
        fig = crear_dispersion()
        grafica.content = fch.MatplotlibChart(figure=fig)
        page.update()
        plt.close(fig)

    page.add(
        ft.Text("Selecciona el tipo de gráfica", size=25, weight="bold"), 

        ft.Row(
            [
                ft.ElevatedButton("Barras", on_click=mostrar_barras),
                ft.ElevatedButton("Circular", on_click=mostrar_circular),
                ft.ElevatedButton("Líneas", on_click=mostrar_lineas),
                ft.ElevatedButton("Dispersión", on_click=mostrar_dispersion),
            ],
            alignment=ft.MainAxisAlignment.CENTER,
        ),

        grafica
    )


ft.app(target=main)
```

## Creación de componentes (visuales y no visuales) definidos por el usuario

Los componentes visuales son aquellos elementos dentro de una aplicación que poseen una representación gráfica, es decir, pueden ser vistos e interactuados por el usuario tanto en el diseño como durante la ejecución del programa. Estos componentes forman parte de la interfaz gráfica y permiten la comunicación directa entre el sistema y el usuario. Entre los ejemplos más comunes de componentes visuales se encuentran los botones, cuadros de texto, imágenes, barras de desplazamiento, listas y etiquetas. Todos estos elementos cumplen la función de mostrar información o permitir la interacción del usuario con la aplicación. A diferencia de los componentes no visuales, los componentes visuales sí tienen una apariencia en pantalla y responden a acciones del usuario, como clics, escritura o selección de opciones. Esta característica los convierte en elementos fundamentales para el diseño de interfaces intuitivas y funcionales.

Tipos de componentes visuales

Los componentes visuales pueden dividirse en dos tipos principales:

-Componentes interactivos: Son aquellos que permiten la interacción directa del usuario. A través de ellos, el usuario puede introducir datos, seleccionar opciones o ejecutar acciones dentro de la aplicación. Ejemplos de estos componentes son los botones, campos de texto y listas desplegables.

-Componentes gráficos: Son aquellos cuyo propósito principal es mostrar información visual, como imágenes, gráficos o textos. Generalmente no requieren interacción directa del usuario, aunque pueden ser manipulados por el programador para actualizar su contenido o apariencia.


**Ejemplo**
En el siguiente código se implementan diversos componentes visuales proporcionados por la librería flet, los cuales permiten construir la interfaz gráfica de una aplicación tipo tienda. Estos componentes son visibles para el usuario y facilitan la interacción con el sistema, ya sea mostrando información o permitiendo realizar acciones.

Uno de los principales componentes visuales utilizados es ft.Container, el cual funciona como un contenedor que agrupa otros elementos en este caso, se utiliza para definir el componente personalizado ProductCard, que representa visualmente cada producto dentro de la tienda. Este contenedor incluye propiedades como tamaño, color de fondo, bordes y sombras, lo que permite mejorar la apariencia del diseño. Dentro de este contenedor se emplea ft.Column, que organiza los elementos de forma vertical, permitiendo estructurar la información del producto de manera ordenada. A su vez, se utilizan múltiples componentes ft.Text para mostrar datos como el identificador, nombre, descripción y precio del producto.

Otro componente visual importante es ft.Image, el cual permite mostrar la imagen correspondiente a cada producto, contribuyendo a una presentación más atractiva. Asimismo, se utilizan componentes interactivos como ft.ElevatedButton, que permite al usuario agregar productos al carrito, y ft.IconButton, que funciona como un botón de favorito representado con un ícono. Además, se emplea ft.Row para organizar elementos de forma horizontal, como en el caso de los botones y el encabezado de la aplicación. También se utiliza ft.TextField, que actúa como un campo de entrada para buscar productos, permitiendo al usuario interactuar directamente con el sistema mediante texto. Otros componentes visuales relevantes incluyen ft.Divider, que sirve para separar secciones dentro de la interfaz, y ft.Page, que representa la estructura principal donde se agregan todos los elementos visuales.

```python
class ProductCard(ft.Container):

    def __init__(self, producto, agregar_carrito):
        super().__init__()

        self.content = ft.Column(
            controls=[

                ft.Text(f"ID: {producto['id']}"),

                ft.Image(
                    src=f"assets/{producto['ruta_imagen']}",
                    width=230,
                    height=150
                ),

                ft.Text(producto["nombre"]),

                ft.Text(producto["descripcion"]),

                ft.Text(f"${producto['precio']:,}"),

                ft.Row(
                    controls=[
                        ft.IconButton(icon=ft.Icons.FAVORITE_BORDER),

                        ft.ElevatedButton(
                            "Agregar al carrito",
                            icon=ft.Icons.SHOPPING_CART
                        )
                    ]
                )
            ]
        )
```

**Componentes no visuales**
Los componentes no visuales son aquellos elementos dentro de una aplicación que no poseen una representación gráfica en la interfaz, es decir, no son visibles para el usuario durante la ejecución del programa. Sin embargo, cumplen funciones esenciales relacionadas con la lógica, el procesamiento de datos y el control del comportamiento del sistema.A diferencia de los componentes visuales, los componentes no visuales pueden colocarse dentro de los formularios o estructuras del programa sin que su posición tenga relevancia, ya que no influyen en el diseño de la interfaz. Su función principal es trabajar en segundo plano, gestionando información y permitiendo que la aplicación responda correctamente a las acciones del usuario.
Estos componentes son fundamentales en el desarrollo de software, ya que permiten separar la lógica del programa de la parte visual, facilitando la organización, el mantenimiento y la escalabilidad del sistema. Gracias a ellos, es posible realizar operaciones como almacenar datos, ejecutar funciones, controlar eventos y procesar información.

