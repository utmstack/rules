# UTMStackCorrelationRules

## Guia para la sintaxis de las reglas.
Note que en cada archivo pueden haber multiples reglas

- name: Some rule name # Este es el nombre que se usara para las alertas de la regla actual
  severity: Low # La severidad que van a atener las alertas de esta regla. Usar la inicial mayuscula. Posibles valores: Low, Medium, High.
  description: Some rule description # Descripcion que aparecera en las alertas
  solution: Some solution # Si existe una solucion conocida para este incidente especificar aqui
  category: Some rule category # Si existe alguna categoria en la que se pueda agrupar la alerta
  tactic: Some tactic # Si un incidente detectado por esta regla encaja en alguna de las tacticas de ataque
  reference: "https://example.com" # Hay alguna URL donde se pueda obtener mas informacion del ataque
  frequency: 15 # Cada que tiempo se debe chequear en segundos
  cache: # Este cajon es para crear un ciclo que sera ejecutado sobre la cache unicamente. Cuando se usa este cajon no se usa el de search y viceversa.
    - allOf: # Todos los operadores dentro de este cajon deben cumplirse
        - field: proto # El field sobre el que se va a aplicar el operador que sigue
          operator: "==" # Los operadores que se pueden aplicar sobre el field son ==, !=, <, >, <= y >=
          value: UDP # Valor con el que se va a comparar el contenido del field. En el caso de la segunda iteracion o en adelante podra usarse un alias para usar el contenido de ese alias como value
      oneOf: # Cualquier operador dentro de este cajon deben cumplirse
        - field: port # Se aplican las mismas caracteristicas que a los field de allOf
          operator: "==" # Se aplican las mismas caracteristicas que a los operator de allOf
          value: 56 # Se aplican las mismas caracteristicas que a los value de allOf
        - field: port # Se aplican las mismas caracteristicas que a los field de allOf
          operator: "==" # Se aplican las mismas caracteristicas que a los operator de allOf
          value: 514 # Se aplican las mismas caracteristicas que a los value de allOf
      timeLapse: 15 # Cuanto tiempo hacia atras se va a revisar en los logs en segundos
      minCount: 1 # Cuantos logs minimo deben obtenerse como resultado para que esta regla se cumpla
      save: # Aca se eligen los fields que se pueden guardar para usar en la siguiente iteracion del ciclo y para completar la informacion de las alertas. El campo alias no debe contener dos o mas alias con el mismo nombre en la misma iteracion. Los alias: Protocol, SourceUser, SourceHost, SourceIP, SourcePort, DestinationUser, DestinationHost, DestinationIP y DestinationPort, seran usados por el sistema de encontrarse en la ultima iteracion para rellenar los detalles de la alerta. Otros detalles como la geolocalizacion seran rellenados automaticamente a partir de los alias SourceIP y DestinationIP. En el caso de que no existan los alias SourceIP o DestinationIP el sistema tratara de generarlos usando SourceHost y DestinationHost y viceversa. Si algun campo se guarda con los alias AlertName y AlertCategory en la ultima iteracion, se sobreescribira el nombre o la categoria de la alerta con el contenido de esos alias segun corresponda.
        - field: proto # El nombre origial del campo que se quiere almacenar
          alias: Protocol # El alias o nombre con el que se podra acceder al campo
  search: # Este cajon es para crear la iteracion que sera ejecutada directamente contra el search engine, cuando se usa este cajon no se usa el de cache y viceversa.
    - query: '' # La query de elasticsearch u opensearch en json format.
      minCount: 1 # Se aplican las mismas caracteristicas que a los minCount del cajon cache
      save: # Se aplican las mismas caracteristicas que en los save del cajon cache
        - field: proto # Se aplican las mismas caracteristicas que a los field de los save del cajon cache
          alias: Protocol # Se aplican las mismas caracteristicas que a los alias de los save del cajon cache


## Cuando usar cache o search
Para las reglas que van a analisar logs en un periodo maximo de 1h usar el cajon cache. Para las que van a consultar mas de 1h de datos usar el cajon search.
