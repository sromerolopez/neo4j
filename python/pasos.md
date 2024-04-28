
## BBDD Python

### 1. Cargamos los nodos:

```console
WITH "https://github.com/neo4j-graph-analytics/book/raw/master/data/sw-nodes.csv" AS uri
LOAD CSV WITH HEADERS FROM uri AS row
MERGE (python:Python {id:row.id})
```

### 2. Cargamos las aristas:

```console
WITH "https://github.com/neo4j-graph-analytics/book/raw/master/data/sw-relationships.csv" AS uri
LOAD CSV WITH HEADERS FROM uri AS row
MATCH (origin:Python {id: row.src})
MATCH (destination:Python {id: row.dst})
MERGE (origin)-[:RELATIONSHIP {relationship: row.relationship}]->(destination)
```



### 2. Cargamos las aristas (MI CSV):

```console
WITH "https://github.com/sromerolopez/neo4j/blob/main/python/aristas.csv" AS uri
LOAD CSV WITH HEADERS FROM uri AS row
MATCH (origin:Python {id: row.src})
MATCH (destination:Python {id: row.dst})
MERGE (origin)-[:RELATIONSHIP {relationship: row.relationship}]->(destination)
```




### 3. Visualizamos lo cargado

```console
MATCH (origin) RETURN (origin)
```

### 4. Creamos el grafo

```console
CALL gds.graph.create(
'myGraph','Python',
{RELATIONSHIP: {
orientation: 'REVERSE'}})
```
