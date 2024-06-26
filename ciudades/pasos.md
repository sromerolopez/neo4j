# BBDD Ciudades


## 1. Carga básica del grafo

### 1.1 Cargamos los nodos:

```console
WITH "https://raw.githubusercontent.com/sromerolopez/neo4j/main/ciudades/nodos.csv" AS uri
LOAD CSV WITH HEADERS FROM uri AS row
MERGE (place:Place {id:row.id})
SET place.latitude = toFloat(row.latitude),
place.longitude = toFloat(row.longitude),
place.population = toInteger(row.population)

```

### 1.2 Cargamos las aristas:

```console
WITH "https://raw.githubusercontent.com/sromerolopez/neo4j/main/ciudades/aristas.csv" AS uri
LOAD CSV WITH HEADERS FROM uri AS row
MATCH (origin:Place {id: row.src})
MATCH (destination:Place {id: row.dst})
MERGE (origin)-[:EROAD {distance: toInteger(row.cost)}]->(destination)
```

### 1.3 Visualizamos lo cargado

```console
MATCH (origin) RETURN (origin)
```

### 1.4 Creamos el grafo

```console
CALL gds.graph.create(
  'myGraph', // El nombre del grafo en GDS
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

## 2. Cálculo de Cáminos Mínimos

### 2.1 Algoritmo de Dijkstra

Ejemplo 1 Doncaster-Utrecht

```console
MATCH (source:Place{id:'Doncaster'})
CALL gds.allShortestPaths.dijkstra.stream('myGraph', {
    sourceNode: source,
    relationshipWeightProperty: 'distance'
})
YIELD index, sourceNode, targetNode, totalCost, nodeIds, costs, path
RETURN
    index,
    gds.util.asNode(sourceNode).id AS sourceNodeName,
    gds.util.asNode(targetNode).id AS targetNodeName,
    totalCost,
    [nodeId IN nodeIds | gds.util.asNode(nodeId).id] AS nodeNames,
    costs,
    nodes(path) as path
ORDER BY index
```
Ejemplo 2 Felixstowe-Utrecht

```console
MATCH (source:Place{id:'Felixstowe'})
CALL gds.allShortestPaths.dijkstra.stream('myGraph', {
    sourceNode: source,
    relationshipWeightProperty: 'distance'
})
YIELD index, sourceNode, targetNode, totalCost, nodeIds, costs, path
RETURN
    index,
    gds.util.asNode(sourceNode).id AS sourceNodeName,
    gds.util.asNode(targetNode).id AS targetNodeName,
    totalCost,
    [nodeId IN nodeIds | gds.util.asNode(nodeId).id] AS nodeNames,
    costs,
    nodes(path) as path
ORDER BY index ASC
```

### 2.2 Algoritmo A*
Ejemplo Felixstowe-Utrecht
```console
MATCH (source:Place{id:'Felixstowe'}), (target:Place{id:'Utrecht'})
CALL gds.shortestPath.astar.stream('myGraph', {
sourceNode:source,
targetNode:target,
latitudeProperty:'latitude',
longitudeProperty:'longitude',
relationshipWeightProperty: 'distance'
})
YIELD index, sourceNode, targetNode, totalCost, nodeIds, costs, path
RETURN
index,
gds.util.asNode(sourceNode).id AS sourceNodeName,
gds.util.asNode(targetNode).id AS targetNodeName,
totalCost,
[nodeId IN nodeIds | gds.util.asNode(nodeId).id] AS nodeNames,
costs,
nodes(path) as path
ORDER BY index
```

## 3 Predicción de enlaces

### 3.1 Vecinos Comunes
```console
MATCH (x:Place{id:'Den Haag'})
MATCH (y:Place{id:'Utrecht'})
RETURN gds.alpha.linkprediction.commonNeighbors(x,y) AS score
```

### 3.2 Adhesión Preferencial
```console
MATCH (x:Place{id:'Den Haag'})
MATCH (y:Place{id:'Utrecht'})
RETURN gds.alpha.linkprediction.preferentialAttachment(x, y) AS score
```

### 3.3 Asignación de Recursos
```console
MATCH (x:Place{id:'Den Haag'})
MATCH (y:Place{id:'Utrecht'})
RETURN gds.alpha.linkprediction.resourceAllocation(x, y) AS score
```
