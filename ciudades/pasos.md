## BBDD Ciudades

### 1. Cargamos los nodos:

```console
WITH "https://github.com/neo4j-graph-analytics/book/raw/master/data/transport-nodes.csv" AS uri
LOAD CSV WITH HEADERS FROM uri AS row
MERGE (place:Place {id:row.id})
SET place.latitude = toFloat(row.latitude),
place.longitude = toFloat(row.longitude),
place.population = toInteger(row.population)

```

### 2. Cargamos las aristas:

```console
WITH "https://github.com/neo4j-graph-analytics/book/raw/master/data/transport-relationships.csv" AS uri
LOAD CSV WITH HEADERS FROM uri AS row
MATCH (origin:Place {id: row.src})
MATCH (destination:Place {id: row.dst})
MERGE (origin)-[:EROAD {distance: toInteger(row.cost)}]->(destination)
```

### 3. Visualizamos lo cargado

```console
MATCH (origin) RETURN (origin)
```

### 4. Creamos el grafo

```console
CALL gds.graph.create(
  'transportGraph', // El nombre del grafo en GDS
  'Place',          // Tipo de nodo, como en tu CSV de nodos
  {
    EROAD: {        // Tipo de relación, como en tu CSV de relaciones
      type: 'EROAD',
      properties: 'distance', // Asume que solo quieres la propiedad 'distance'
      orientation: 'NATURAL'  // Puedes elegir 'REVERSE' si lo necesitas
    }
  },
  {
    nodeProperties: ['latitude', 'longitude', 'population'] // Propiedades del nodo que quieres incluir
  }
)

```
