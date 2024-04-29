
# BBDD Python

## 1. Carga básica del grafo

### 1.1 Cargamos los nodos:

```console
WITH "https://raw.githubusercontent.com/sromerolopez/neo4j/main/python/nodos.csv" AS uri
LOAD CSV WITH HEADERS FROM uri AS row
MERGE (python:Python {id:row.id})
```

### 1.2 Cargamos las aristas:

```console
WITH "https://raw.githubusercontent.com/sromerolopez/neo4j/main/python/aristas.csv" AS uri
LOAD CSV WITH HEADERS FROM uri AS row
MATCH (origin:Python {id: row.src})
MATCH (destination:Python {id: row.dst})
MERGE (origin)-[:RELATIONSHIP {relationship: row.relationship}]->(destination)
```

### 1.3 Visualizamos lo cargado

```console
MATCH (origin) RETURN (origin)
```


## 2. Algoritmos Medidas de Centralidad

### 2.1 Creamos el grafo para aplicar estos algoritmos

```console
CALL gds.graph.create(
'myGraph','Python',
{RELATIONSHIP: {
orientation: 'REVERSE'}})
```

### 2.2 Algoritmo básico de centralidad

```console
CALL gds.degree.stream('myGraph')
YIELD nodeId, score
RETURN gds.util.asNode(nodeId).id AS id, score AS RELATIONSHIP
ORDER BY RELATIONSHIP DESC, id DESC
```

### 2.3 Algoritmo de cercanía (centralidad)
```console
CALL gds.alpha.closeness.stream({
nodeProjection: 'Python',
relationshipProjection: 'RELATIONSHIP'
})
YIELD nodeId, centrality
RETURN gds.util.asNode(nodeId).id AS Python, centrality
ORDER BY centrality DESC
```

### 2.4 Algoritmo de intermediación (centralidad)
```console
CALL gds.betweenness.stream('myGraph')
YIELD nodeId, score
RETURN gds.util.asNode(nodeId).id AS id, score
ORDER BY score DESC
```

## 3. Algoritmos de Comunidades

### 3.1 Creamos el grafo para estos algoritmos
```console
CALL gds.graph.create(
'myGraph2',
'Python',
{
RELATIONSHIP: {
orientation: 'UNDIRECTED'
}
}
)
```

### 3.2 Conteo de Triángulos
```console
CALL gds.triangleCount.stream('myGraph2')
YIELD nodeId, triangleCount
RETURN gds.util.asNode(nodeId).id
AS id, triangleCount
ORDER BY triangleCount DESC
```

### 3.3 Coeficiente local de clustering
```console
CALL gds.localClusteringCoefficient.stream('myGraph2')
YIELD nodeId, localClusteringCoefficient
RETURN gds.util.asNode(nodeId).id
AS id, localClusteringCoefficient
ORDER BY localClusteringCoefficient DESC
```


