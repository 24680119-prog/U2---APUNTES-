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
**Libreria matplotlib**
