![Logo Sharks attacks](https://github.com/crisgo-data/data-cleaning-sharks-attack/blob/main/imagenes/sHARKS.png"Project to present")

En este proyecto tenemos como objetivo saber el porcentaje de ataques de tiburones que acaban en muerte en USA y su variación por decadas. Para ello vamos a limpiar los datos de la tabla, tomando decisiones y dando una respuesta final a esta pregunta.

1. En primer lugar importamos todas las bibliotecas que necesitemos así como el archivo ZIP del cual obtendremos nuestro DataFrame original.
2. Hago un examen exhaustivo de los datos viendo su shape, el nombre de las columnas, veo su Dtype, su Info (NonNull) y su Describe (Media,Mediana,Moda...)
Tambien utilizo un Check_nan para que me muestre los valores Nan es una tabla.

3. Para empezar con la limpieza de datos quiero saber que porcentaje de Nan tiene cada columna.
4. Me doy cuenta que de la columna 6297 para abajo no hay registros por lo que eliminio todas las correspondientes.

5. También elimino las filas duplicadas con .droopduplicates().

6. Mi objetivo se centra generalmente en 3 columnas que debo dejar bastante limpias y con el mayor número de filas posibles para que sea más exacto.
Estas columnas son 'Country' 'Year' y 'Fatal (Y/N)'


7.Hay varias columnas que se podrían rellenar directamente con 'Unkown' pero hay otras que se correlacionan entre ellas y podrían rellenarse con datos verdaderos. Es el caso de la columna 'Date'.

DATE

He limpiado la columna Date ya que era bastante inconsistente. Mediante Regex busqué los valores que podían contener una fecha, Los valores que contienen ciertos datos sin una fecha concreta por ejemplo 'World War II' o  '1939-1945', los remplazo con un datetime y los que no sean posibles los dejo como Nat.

CASE NUMBER

Es una columna ID, por lo que sustituyo todos sus valores por indices del 1 al n.


YEAR

He llegado a una de mis columnas objetivo. Convierto a ceros los años que no tienen información, y los sustituyo por el año que hemos sacado en la columna Date. Lo hago mediante la función .apply.

Me doy cuenta que siguen habiendo valores NaN en la columna 'Year' mediante el info de NonNull, por lo que los convierto en 'unknown'.

Veo que los valores nulos de la columna Date y Year son los mismos y el número es pequeño (93) por lo que puedo eliminar esas filas.
Ahora puedo convertir los años a enteros con .astype(int).
Ya tengo todos mis datos con el año.



---->Ahora me fijo en otras columnas que puedan estar relacionadas entre ellas, por ejemplo 'Area' y 'Location' y 'Country'".
En las columnas que tienen un bajo porcentaje de valores nulos directamente los convierto a 'Unknown' para poder trabajar con ellos: por ejemplo la columna 'Country' tiene un porcentaje muy bajo y la necesito para poder trabajar las columnas 'Area' y 'Location'.


AREA

Relleno la columna Area a traves de la columna  Country, por lo que necesito que los valores Nulos se conviertan en 'Unknown'. Después calculo la moda de los valores de Area en Country mediante .gropuby y generando un diccionario puedo rellenar los valores de Área con la moda de los valores únicos de country.


----> Observo que la columna 'Type', 'Name', 'Injury' 'Investigator Source' y 'href formula' 
tiene un porcentaje de nulos muy bajo por lo que puedo rellenar con unknows de momento.


FATAL (Y/N)

Ahora para mi columna Objetivo, veo que muchos de sus valores nulos, pueden ser rellenos a partir de la columna 'Injury'. Para ello creo una función Lambda para recorrer toda la columna 'Injury' y si la palabra FATAL,
está incluida en la columna, entonces me rellena con Y y sino con N.


SEX

Podemos aplicar el mismo codigo para rellenar la columna 'Sex'
atraves de la columna 'pdf' la cual contiene algún dato que nos ayuda a saber el sexo, como palabra Boy 
o girl.


LOCATION

Para limpiar la columna Location podríamos limpiarla a través de la columna Area, al igual que hicimos en Country
cogiendo su Moda. En este caso debemos rellenar con Unknown los valores de Nan de la columna 'Location' Para poder trabajar con ellos.




ACTUALMENTE CON LOS CAMBIOS HECHOS TENGO UN SHAPE = 6118 rows × 24 columns




ACTIVITY

No encuentro correlación de la columna Activity con otras columnas por lo que la rrelleno con 'Unknown'.



UNNAMED 22 Y 23

Estas columnas  no contienen información relevante, las rellenamos con Unknown ya que no podemos eliminarlas.



AGE

Mi idea es calcular la media de la edad de hombres y mujeres para rellenar los Nan de esta columna.
Para ello debo eliminar primero los Outliners. 
Veo que en los dos caso la media son 27 años por lo que podemos sustituir los valores Nan por este dato.


TIME

Esta es una fila que genera muchos problemas ya que hay gran variedad de formato dentro de sus datos. 
Me he fijado que hay un formato 'Numero-h-numero' Otro que es 'Morning' otro 'Afternoon', otro 'night', otro 'Late afternoon'... Creo la siguiente función:

def convert_to_time(value):

    try:
    
        # intentar convertir a float
        
        float_value = float(value)
        
        # calcular horas, minutos y segundos
     
        seconds = int(float_value)
        hours = seconds // 3600
        minutes = (seconds % 3600) // 60
        return "{:02d}:{:02d}:{:02d}".format(hours, minutes, seconds)
        
    except ValueError:
        if isinstance(value, str):
            if "h" in value:
                # formato numero-h-numero
                parts = value.split("h")
                return "{}:00:00".format(parts[0].zfill(2))
            else:
                if value == "Morning":
                    return "10:00:00"
                elif value == "Afternoon":
                    return "17:00:00"
                elif value == "Night":
                    return "20:00:00"
                elif value == "Late afternoon":
                    return "18:00:00"
    return value


Despues aplico esa función a toda la columna, lo cual me la deja toda en el formato Horas:minutos:segundos.

Los valores que no se han modificado los relleno con Unknown.





SPECIES

No encuentro ninguna correlación con otras columnas por lo que la relleno con 'Unknown'.




----->Ahora que he llegado casi al final de la limpieza me fijo en las filas que puedan tener un Porcentaje por encima del 50% de Unknown en sus datos y las elimino. En este caso no hay ninguna fila que cumpla la condición por lo que sigo con mi Shape anterior: 6118 rows × 24 columns





Vuelvo a mis columnas objetivo y me fijo en la cantidad de Unknowns y datos inconsistentes que tiene cada uno, los localizo por fila y al ser un porcentaje muy bajo directamente las elimino para no tener datos faltantes.


Al terminar mi limpieza mi SHAPE final es: (6008, 24)



La respuesta a nuestro Objetivo:


Porcentaje de ataques fatales en USA: 9.036697247706423 %


