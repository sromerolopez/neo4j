# neo4j
Códigos necesarios para la parte práctica de Neo4j

# BBDD Python

##1. Cargamos los nodos:

```console
WITH "https://github.com/neo4j-graph-analytics/book/raw/master/data/sw-nodes.csv" AS uri
LOAD CSV WITH HEADERS FROM uri AS row
MERGE (python:Python {id:row.id})
```

##2. Cargamos las aristas:

WITH "https://github.com/neo4j-graph-analytics/book/raw/master/data/sw-relationships.csv" AS uri
LOAD CSV WITH HEADERS FROM uri AS row
MATCH (origin:Python {id: row.src})
MATCH (destination:Python {id: row.dst})
MERGE (origin)-[:RELATIONSHIP {relationship: row.relationship}]->(destination)
