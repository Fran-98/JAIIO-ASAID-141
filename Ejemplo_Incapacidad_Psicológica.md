# Incapacidad Psicológica
## Definiendo la Entidad
Para formatear los textos se utilizó una estructura que los modelos pudieran entender fácilmente y que luego sea fácil de parsear, la misma idea que se toma para el _function calling_. Este texto en forma de JSON va al principio del _prompt_.

Definimos una función/clase que será la entidad:

```python
IncapacidadPsicologica = {
            "function": "incapacidad_psicologica",
            "description": "Incapacidad Psicologica otorgado por el juez. Funcion a devolver si se encuentra una incapacidad médica explicitamente mencionada en el texto y estas totalmente seguro que es una incapacidad de una persona, en caso de no existir incapacidad en el texto devolver esta funcion con los argumentos en null",
            "arguments": [
                {
                    "hay_incapacidad": "Si se encuentra que el sujeto sufre una incapacidad psicologica devolver true. Valor por default= false",
                    "porcentaje": 'Porcentaje de incapacidad psicologica otorgado por el juez de primera instancia. Suele aparecer en el párrafo “DAÑOS” acápite de ”incapacidad sobreviniente” “daño sobreviniente”, “daño psicologico”. Tomar el último % que aparezca en el texto. Si no se menciona en el texto devolver el valor null',
                    "monto_indemnizatorio": 'Si prospera el monto de la indemnización o el juez determina un monto por incapacidad. Si no se menciona en el texto devolver el valor null'
                    
                }
            ]
        }
```

## _Prompt_ de extracción

Se tiene un mensaje de extracción genérico para todas las incapacidades y luego se le dice en específico que busca.

```python
_MENSAJE_INCAPACIDAD_PSICOLOGICA_EXTRACCION = """Eres un experto extractor de datos. \
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

Se extraerá si el sujeto sufre una incapacidad psicológica, \
esta acompañada por un porcentaje de incapacidad psicologica y un monto indemnizatorio por incapacidad, solo extraer \
el porcentaje y monto si estos se encuentran explicitamente en el texto.

TEXTO: La actora formula reclamo por la suma de pesos ciento cincuenta mil ($150.000) por daño físico y lucro \
cesante y la de pesos ochenta mil ($80.000) por daño psicológico y tratamiento psicoterapéutico.\n Conforme adelantara, \
a fs.252/253 obran las constancias referidas a la atención médica que se le brindara a la accionante en el "Hospital J.M. \
Ramos Mejía".\nEl cuadro encuadra en un desarrollo reactivo a los acontecimientos que motivan las presentes actuaciones. \n\n
Se lo incluye dentro del item 2.6.5 de grado moderado y se le adjudica un 10% de incapacidad. Recomiendo un tratamiento \
psicologico con el proposito de evitar el agrvamiento del cuadro presente. Si bien resulta dificil establecer la duracion del mismo \
, ya que depende de la raccion de cada sujeto a la psicoterapia, estimo que el tratamiento sugerido deberá tener una extensión de al menos \
un año, a una frecuencia de una sesión por semana.\n\n

EXTRACCION:{"function": "incapacidad_psicologica","arguments":{"hay_incapacidad": true,"porcentaje": 10,"monto_indemnizatorio": 80000}}
"""
```