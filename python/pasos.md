
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
Análisis del coeficiente local de clustering nos permite ver que la librería con mayor puntuación y por tanto que agrupa a más librerías en torno a ella es “ipykernel”, tiene un coeficiente de 1 que es el mayor coeficiente local de clustering.

```console
CALL gds.localClusteringCoefficient.stream('myGraph2')
YIELD nodeId, localClusteringCoefficient
RETURN gds.util.asNode(nodeId).id
AS id, localClusteringCoefficient
ORDER BY localClusteringCoefficient DESC
```

### 3.4 Componentes fuertemente conexas
Análisis de componentes fuertemente conexas, que analiza cuando un nodo puede ser alcanzado por cualquier otro nodo en ambas direcciones y por tanto nos permite estudiar la conectividad de la red.

```console
CALL gds.alpha.scc.stream({
nodeProjection: 'Python',
relationshipProjection: 'RELATIONSHIP'
})
YIELD nodeId, componentId
RETURN gds.util.asNode(nodeId).id AS id, componentId AS Component
ORDER BY Component DESC
```
