# Reglas de correlacion

## Referencia de los campos

### name
Este es el nombre que se usara para las alertas de la regla actual

### severity
La severidad que van a atener las alertas de esta regla. Usar la inicial mayuscula. Posibles valores: Low, Medium, High.

### description
Descripcion que aparecera en las alertas

### solution
Si existe una solucion conocida para este incidente especificar aqui

### category
Si existe alguna categoria en la que se pueda agrupar la alerta

### tactic
Si un incidente detectado por esta regla encaja en alguna de las tacticas de ataque

### reference
Lista de URLs donde se pueda obtener mas informacion sobre el ataque

### frequency
Cada que tiempo se debe chequear en segundos

### cache
Este campo declara que las iteraciones ocurriran sobre la cache del correlation engine y encierra la definicion de dichas iteraciones. Cuando se usa este campo no se usa el de search y viceversa.

### cache -> allOff
Todas las comparaciones dentro de este campo deben cumplirse para que la regla genere una alerta

### cache -> oneOff
Cualquier comparacion dentro de este campo debe cumplirse para que la regla genere una alerta

### cache -> [allOff | oneOff] -> field
El field sobre el que se va a aplicar la comparacion.

Un field es una serie de claves separadas por puntos. Puede contener caracteres comodín especiales '*' y '?'. Para acceder a un valor de un slice, use el índice como clave. Para obtener el número de elementos en un slice o para acceder a una ruta secundaria, use el caracter '#'. Los caracteres de punto y comodín se pueden escapar con '\\'.

También puede consultar un slice para la primera coincidencia usando #(...), o buscar todas las coincidencias con #(...)#. Se admiten los operadores de comparación ==, !=, <, <=, >, >= y los operadores de coincidencia de patrones simples % y !%

Esta descripcion aplica tambien para [cache | search] -> save -> field

```
{
  "name": {"first": "Tom", "last": "Anderson"},
  "age":37,
  "children": ["Sara","Alex","Jack"],
  "fav.movie": "Deer Hunter",
  "friends": [
    {"first": "Dale", "last": "Murphy", "age": 44, "nets": ["ig", "fb", "tw"]},
    {"first": "Roger", "last": "Craig", "age": 68, "nets": ["fb", "tw"]},
    {"first": "Jane", "last": "Murphy", "age": 47, "nets": ["ig", "tw"]}
  ]
}

"name.last"                           >> "Anderson"
"age"                                 >> 37
"children"                            >> ["Sara","Alex","Jack"]
"children.#"                          >> 3
"children.1"                          >> "Alex"
"child*.2"                            >> "Jack"
"c?ildren.0"                          >> "Sara"
"fav\.movie"                          >> "Deer Hunter"
"friends.#.first"                     >> ["Dale","Roger","Jane"]
"friends.1.last"                      >> "Craig"
"friends.#(last=="Murphy").first"     >> "Dale"
"friends.#(last=="Murphy")#.first"    >> ["Dale","Jane"]
"friends.#(age>45)#.last"             >> ["Craig","Murphy"]
"friends.#(first%"D*").last"          >> "Murphy"
"friends.#(first!%"D*").last"         >> "Craig"
"friends.#(nets.#(=="fb"))#.first"    >> ["Dale","Roger"]
```

### cache -> [allOff | oneOff] -> operator
Operador que se va a usar en la comparacion. Ver informacion sobre los operadores en [Operadores](https://github.com/quantfall/UTMStackCorrelationRules/blob/master/README-Spanish.md#operadores)

### cache -> [allOff | oneOff] -> value
Valor con el que se va a comparar el contenido de "cache -> [allOff | oneOff] -> field". En el caso de la segunda iteracion o en adelante podra usarse un alias para usar el contenido de ese alias como value.

### cache -> timeLapse
Cuanto tiempo hacia atras se va a revisar en los logs en segundos

### [cache | search] -> minCount
Cuantos logs minimo deben obtenerse como resultado para que esta regla se cumpla

### [cache | search] -> save
Fields que se quieren guardar para usar en la siguiente iteracion del ciclo o para completar la informacion de las alertas.

### [cache | search] -> save -> field
El nombre origial del campo que se quiere almacenar

### [cache | search] -> save -> alias
El alias o nombre con el que se podra acceder al campo

No pueden existir dos o mas alias con el mismo nombre en la misma iteracion. 

Los siguientes alias seran usados por el sistema de encontrarse en la ultima iteracion para rellenar los detalles de la alerta: 

* Protocol
* SourceUser
* SourceHost
* SourceIP
* SourcePort
* DestinationUser
* DestinationHost
* DestinationIP
* DestinationPort

Otros detalles como la geolocalizacion seran rellenados automaticamente a partir de los alias SourceIP y DestinationIP. 

En el caso de que no existan los alias SourceIP o DestinationIP el sistema tratara de generarlos usando SourceHost y DestinationHost y viceversa. 

Si algun campo se guarda con los alias AlertName y AlertCategory en la ultima iteracion, se sobreescribira el nombre o la categoria de la alerta con el contenido de esos alias segun corresponda.

### search 
Este campo declara que las iteraciones ocurriran sobre Elasticsearch y encierra la definicion de dichas iteraciones. Cuando se usa este campo no se usa el de cache y viceversa.

### search -> query
La query de elasticsearch u opensearch en json format. Recuerde encerrar la query entre comillas simples.

## Operadores

### ==
Es verdadero si el contenido del field es exactamente igual al contenido de "value", sencible a las mayusculas
* hello == Hello //False
* hello == hello //True

### ::
Es verdadero si el contenido del field es igual al contenido de "value", no sencible a las mayusculas
* hello :: Hello //True
* hello :: hello //True

### != y <>
Es verdadero si el contenido del field es diferente al contenido de "value", sencible a las mayusculas
* hello != Hello //True
* hello != hello //False

### !!
Es verdadero si el contenido del field es diferente al contenido de "value", no sencible a las mayusculas
* hello !! Hello //False
* hello !! hello //False

### contains
Es verdadero si el contenido de "value" es parte del contenido del field
* "hola mundo" contains "mundo" //True
* "hola mundo" contains "mundos" //False

### not contain
Es verdadero si el contenido de "value" no es parte del contenido del field
* "hola mundo" not contain "mundo" //False
* "hola mundo" not contain "mundos" //True

### in
Es verdadero si el contenido del field es parte del contenido de "value"
* "mundo" in "hola, mundo, esto, es, una, prueba" //True
* "mundos" in "hola, mundo, esto, es, una, prueba" //False

### not in
Es verdadero si el contenido del field no es parte del contenido de "value"
* "mundo" in "hola, mundo, esto, es, una, prueba" //False
* "mundos" in "hola, mundo, esto, es, una, prueba" //True

### start with
Es verdadero si el contenido de "value" es prefijo del contenido del field
* "hola mundo" start with "mundo" //False
* "hola mundo" start with "hola" //True

### not start with
Es verdadero si el contenido de "value" no es prefijo del contenido del field
* "hola mundo" not start with "mundo" //True
* "hola mundo" not start with "hola" //False

### end with
Es verdadero si el contenido de "value" es sufijo del contenido del field
* "hola mundo" end with "mundo" //True
* "hola mundo" end with "hola" //False

### not end with
Es verdadero si el contenido de "value" no es sufijo del contenido del field
* "hola mundo" not end with "mundo" //False
* "hola mundo" not end with "hola" //True

### regexp
Es verdadero si el contenido del field concuerda con la expresion regular de "value"
* "adam[23]" regexp "^[a-z]+\\[[0-9]+\\]$" //True
* "hola mundo" regexp "^[a-z]+\\[[0-9]+\\]$" //False

### not regexp
Es verdadero si el contenido del field no concuerda con la expresion regular de "value"
* "adam[23]" not regexp "^[a-z]+\\[[0-9]+\\]$" //False
* "hola mundo" not regexp "^[a-z]+\\[[0-9]+\\]$" //True

### exist
Es verdadero si existe el field. El value debe quedar vacio, pues debe existir pero no se va a utilizar

### not exist
Es verdadero si no existe el field. El value debe quedar vacio, pues debe existir pero no se va a utilizar

### in cidr
Es verdadero si la IP en field esta en el rango de value

### not in cidr
Es verdadero si la IP en field no esta en el rango de value

### Operadores matematicos clasicos que solo aplican a numeros. Usar solo cuando el contenido del field y el "value" son numericos.
* <
* \>
* <=
* \>=

## Cuando usar cache o search
Recomendamos usar la cache para las reglas que van a analisar logs en un periodo maximo de 1h.

Recomendamos usar search cuando el periodo de analisis excede 1h o la complejidad de la regla es muy alta y aun no existe forma de hacerla usando la cache.

## Ejemplos

~~~
- name: Windows authentication failure
  severity: Low
  description: A Windows user was unable to authenticate with the server or workstation more than 5 times.
  solution: Refer to NIST guidelines when creating password policies and set account lockout policies after a certain number of failed login attempts to prevent passwords from being guessed. Too strict a policy may create a denial of service condition and render environments un-usable, with all accounts used in the brute force being locked-out.
  category: User Account Authentication
  tactic: "Brute Force: Password Guessing"
  reference:
    - "https://attack.mitre.org/techniques/T1110/001/"
  frequency: 10
  cache:
    - allOf:
        - field: logx.wineventlog.event_id
          operator: "=="
          value: 4625
      minCount: 1
      timeLapse: 15
      save:
        - field: logx.wineventlog.event_data.TargetUserName
          alias: DestinationUser
        - field: logx.wineventlog.host.name
          alias: DestinationHost
    - allOf:
        - field: logx.wineventlog.event_id
          operator: "=="
          value: 4625
        - field: logx.wineventlog.event_data.TargetUserName
          operator: "=="
          value: "{{.DestinationUser}}"
        - field: logx.wineventlog.host.name
          operator: "=="
          value: "{{.DestinationHost}}"
      minCount: 5
      timeLapse: 60
      save:
        - field: logx.wineventlog.event_data.TargetUserName
          alias: DestinationUser
        - field: logx.wineventlog.host.name
          alias: DestinationHost
        - field: logx.wineventlog.event_data.WorkstationName
          alias: SourceHost
        - field: logx.wineventlog.event_data.IpAddress
          alias: SourceIP
~~~

~~~
- name: Windows authentication failure
  severity: Low
  description: A Windows user was unable to authenticate with the server or workstation more than 5 times.
  solution: Refer to NIST guidelines when creating password policies and set account lockout policies after a certain number of failed login attempts to prevent passwords from being guessed. Too strict a policy may create a denial of service condition and render environments un-usable, with all accounts used in the brute force being locked-out.
  category: User Account Authentication
  tactic: "Brute Force: Password Guessing"
  reference: 
    - "https://attack.mitre.org/techniques/T1110/001/"
  frequency: 10
  search:
    - query: '{"size": 500, "query": {"bool": {"must": [{"match_phrase": {"logx.wineventlog.event_id": 4625}}], "filter": [{"range": {"@timestamp": {"gte": "now-15s", "lte": "now"}}}], "should": [], "must_not": []}}}'
      minCount: 1
      save:
        - field: logx.wineventlog.event_data.TargetUserName
          alias: DestinationUser
        - field: logx.wineventlog.host.name
          alias: DestinationHost
    - query: '{"size": 500, "query": {"bool": {"must": [{"match_phrase": {"logx.wineventlog.event_id": 4625}}, {"match_phrase": {"logx.wineventlog.event_data.TargetUserName": "{{.DestinationUser}}"}}], "filter": [{"range": {"@timestamp": {"gte": "now-60s", "lte": "now"}}}], "should": [], "must_not": [{"match_phrase": {"logx.wineventlog.event_data.TargetUserName": "{{.DestinationHost}}$"}}]}}}'
      minCount: 5
      save:
        - field: logx.wineventlog.event_data.TargetUserName
          alias: DestinationUser
        - field: logx.wineventlog.host.name
          alias: DestinationHost
        - field: logx.wineventlog.event_data.WorkstationName
          alias: SourceHost
        - field: logx.wineventlog.event_data.IpAddress
          alias: SourceIP
~~~
