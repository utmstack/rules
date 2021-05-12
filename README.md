# UTMStackCorrelationRules

## Guia para la sintaxis de las reglas.

- name: Some rule name # Este es el nombre que se usara para las alertas
  severity: Low # La severidad que van a atener las alertas de esta regla
  description: Some rule description # Descripcion que aparecera en las alertas
  solution: Some solution # Si existe una solucion conocida para este incidente especificar aqui
  category: Some rule category # Si existe alguna categoria en la que se pueda agrupar la alerta
  tactic: Some tactic # Si un incidente detectado por esta regla encaja en alguna de las tacticas de ataque
  reference: "https://example.com" # Hay alguna URL donde se pueda obtener mas informacion del ataque
  frequency: 15 # Cada que tiempo se debe chequear
  cache: # Este cajon es para crear la iteracion que sera ejecutada sobre la cache unicamente. Cuando se usa este cajon no se usa el de search y viceversa.
    - allOf: # Todos los operadores dentro de este cajon deben cumplirse
        - field: proto # El field sobre el que se va a aplicar el operador que sigue
          operator: "==" # Los operadores que se pueden aplicar sobre el field son ==, !=, <= y >=
          value: UDP # Valor con el que se va a comparar el contenido del field
        - field: port # El field sobre el que se va a aplicar el operador que sigue
          operator: "==" # Los operadores que se pueden aplicar sobre el field son ==, !=, <= y >=
          value: UDP # Valor con el que se va a comparar el contenido del field
      oneOf: # Cualquier operador dentro de este cajon deben cumplirse
        - field: proto # El field sobre el que se va a aplicar el operador que sigue
          operator: "==" # Los operadores que se pueden aplicar sobre el field son ==, !=, <= y >=
          value: UDP # Valor con el que se va a comparar el contenido del field
        - field: port # El field sobre el que se va a aplicar el operador que sigue
          operator: "==" # Los operadores que se pueden aplicar sobre el field son ==, !=, <= y >=
          value: UDP # Valor con el que se va a comparar el contenido del field
      timeLapse: 15 # Cuanto tiempo hacia atras se va a revisar en los logs
      minCount: 1 # Cuantos logs minimo deben salir como resultado para que esta regla se cumpla
      save: # Aca se eligen los fields que se pueden guardar para usar en la siguiente iteracion y para completar la informacion de las alertas. El campo alias no debe contener dos o mas alias con el mismo nombre en la misma iteracion. Los alias: Protocol, SourceUser, SourceHost, SourceIP, SourcePort, DestinationUser, DestinationHost, DestinationIP y DestinationPort seran usados por el sistema para rellenar los detalles de la alerta.
        - field: proto # El nombre origial del campo que se quiere almacenar
          alias: Protocol # El alias o nombre con el que se podra acceder al campo
  search: # Este cajon es para crear la iteracion que sera ejecutada directamente contra el search engine, cuando se usa este cajon no se usa el de cache y viceversa.
    - query: '' # La query de elasticsearch u opensearch en json format.
      minCount: 1 # Cuantos logs minimo deben salir como resultado para que esta regla se cumpla
      save: # Aca se eligen los fields que se pueden guardar para usar en la siguiente iteracion y para completar la informacion de las alertas. El campo alias no debe contener dos o mas alias con el mismo nombre en la misma iteracion. Los alias: Protocol, SourceUser, SourceHost, SourceIP, SourcePort, DestinationUser, DestinationHost, DestinationIP y DestinationPort seran usados por el sistema para rellenar los detalles de la alerta.
        - field: proto # El nombre origial del campo que se quiere almacenar
          alias: Protocol # El alias o nombre con el que se podra acceder al campo


## Cuando usar cache o search

Para las reglas que van a analisar logs en un periodo maximo de 1h usar el cajon cache. Para las que van a consultar mas de 1h de datos usar el cajon search.