# Incapacidad Física
## Definiendo la Entidad
Para formatear los textos se utilizó una estructura que los modelos pudieran entender fácilmente y que luego sea fácil de parsear, la misma idea que se toma para el _function calling_. Este texto en forma de JSON va al principio del _prompt_.

Definimos una función/clase que será la entidad:

```python
IncapacidadFisica = {
            "entity": "incapacidad_fisica",
            "description": "Incapacidad física del sujeto, el juez otorga un porcentaje de incapacidad. Suele aparecer en el párrafo “DAÑOS” acápite de ”incapacidad sobreviniente” “daño sobreviniente”, “daño físico”.",
            "arguments": [
                {
                    "hay_incapacidad": "Si se encuentra que el sujeto sufre una incapacidad fisica devolver true. Default = false",
                    "porcentaje": 'Esta entidad corresponde al último porcentaje considerado dentro de los apartados "daño físico", "incapacidad sobreviniente" o "daño sobreviniente". Se debe extraer el porcentaje específico que determina la pericia médica, tomar el porcentaje total indicado. Si no se menciona en el texto devolver el valor null',
                    "monto_indemnizatorio": 'Si prospera el monto de la indemnización o el juez determina un monto por incapacidad. Si no se menciona en el texto devolver el valor null'
                    
                }
            ]
        }
```

## _Prompt_ de extracción

Se tiene un mensaje de extracción genérico para todas las incapacidades y luego se le dice en específico que busca.

```python
_MENSAJE_INCAPACIDAD_FISICA_EXTRACCION = """Eres un experto extractor de datos. \
Leerás diferentes segmentos relacionados entre si de una misma sentencia judicial. \
Tu tarea es buscar y guardar la entidad que se te pasa, \
junto a sus propiedades. \
Solo incluir informacion que se \
encuentre explicitamente mencionada en el texto, en caso de no encontrar la información devolver el campo correspondiente como null o False segun corresponda. \
Tus respuestas siempre son un formato JSON, de esta forma: {"function": nombreDeLaFuncion,"arguments":{"hay_incapacidad": bool,"porcentaje": int,"monto_indemnizatorio": int}}\n
La lógica que debes seguir es la siguiente:
1. Buscar la incapacidad en el texto de la sentencia.
2. Si hay incapacidad y se considera que la persona la sufre entonces hay incapacidad true.
3. Si esta mencionado en porcentaje de incapacidad extraerlo.
4. Si esta mencionado el monto indemnizatorio por esa incapacidad extraerlo.

Se extraerá si el sujeto sufre una incapacidad física, \
esta acompañada por un porcentaje de incapacidad fisica o sobreviniente y un monto indemnizatorio por incapacidad, solo extraer \
el porcentaje y monto si estos se encuentran explicitamente en el texto.

TEXTO: Considero indemnizar por la suma de pesos ciento cincuenta mil ($150.000) por daño físico y lucro \
cesante y la de pesos ochenta mil ($80.000) por daño psicológico y tratamiento psicoterapéutico.\n Conforme adelantara, \
a fs.252/253 obran las constancias referidas a la atención médica que se le brindara a la accionante en el "Hospital J.M. \
Ramos Mejía".\n\n Hace saber el perito que se llevo a cabo el examen de la Sra. Di Paolo y al contestar los puntos \
periciales arriba a la siguiente conclusión "Actualmente se considera que la secuela de la probable fractura de coxis \
que no se ha constatado en los estudios realizados, ya se encuentra consolidada. Este perito considera que la actora \
presenta una secuela de carácter parcial y permanente de un 8% de la total\" \

EXTRACCION:{"function": "incapacidad_fisica","arguments":{"hay_incapacidad": true,"porcentaje": 8,"monto_indemnizatorio": 150000}}
"""
```