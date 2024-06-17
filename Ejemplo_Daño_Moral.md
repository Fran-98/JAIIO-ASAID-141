# Daño Moral
## Definiendo la Entidad
Para formatear los textos se utilizó una estructura que los modelos pudieran entender fácilmente y que luego sea fácil de parsear, la misma idea que se toma para el _function calling_. Este texto en forma de JSON va al principio del _prompt_.

Definimos una función/clase que será la entidad:

```python
dañoMoral = {
            "entity": "incapacidad_psicologica",
            "description": "Daño moral ocasionado, se indemniza mediante un monto de dinero, suele estar al final del apartado Daño Moral",
            "arguments": [
                {
                    "hay_daño_moral": "Si se encuentra daño moral en el texto, solo devolver verdadero si esta explicitamente mencionado",
                    "monto_indemnizatorio": 'Este monto representa la indemnización por el daño moral ocasionado. Usualmente aparece al final del apartado "Daño Moral". En caso de no encontrarse este apartado, se debe verificar la existencia del apartado "daño extrapatrimonial" y extraer el monto de allí.',                    
                }
            ]
        }
```

## _Prompt_ de extracción

El daño moral es un poco diferente a las incapacidades, ya que solo es un monto. Además, es mucho mas fácil de extraer ya que suele estar solo y bien declarado. Gracias a lo anterior, no fue necesario crear prompts mas avanzados con ejemplos o pensar paso a paso.

```python
_MENSAJE_DAÑO_MORAL_EXTRACCION = """Se te pasarán diferentes segmentos de una sentencia judicial. \
Tu tarea es extraer y guardar la entidad que se te entrega como función. \
junto a sus propiedades. Si una de las propiedades no esta presente, no agregarla en el output. En el caso \
de los porcentajes siempre buscar los ultimos porcentajes mencionados. Solo incluir informacion que se \
encuentre explicitamente mencionada en el texto. La información que sale de los textos que se encuentran mas abajo \
es mas importante que lo de mas arriba. Se puede encontrar como daño moral o perjuicio moral o de alguna otra forma.
"""
```