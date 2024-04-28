
## BBDD Python

### 1. Cargamos los nodos:

```console
WITH "https://raw.githubusercontent.com/sromerolopez/neo4j/main/python/nodos.csv" AS uri
LOAD CSV WITH HEADERS FROM uri AS row
MERGE (python:Python {id:row.id})
```

### 2. Cargamos las aristas:

```console
WITH "https://raw.githubusercontent.com/sromerolopez/neo4j/main/python/aristas.csv" AS uri
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
