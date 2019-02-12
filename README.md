# Neo4j

## Link para instalarlo en linux

* https://www.youtube.com/watch?v=k3h_y9w_7l4

## Hacer un cliente con node.js

* https://neo4j.com/developer/javascript/
* https://www.youtube.com/watch?v=snjnJCZhXUM

## Plugin para el server de Neo4j para que levante un Graphql usando el schema de grafos

* https://github.com/neo4j-graphql/neo4j-graphql

(Se puede consultar con el cliente graphiql https://electronjs.org/apps/graphiql)

## En la carpeta migrations hay unos csv para importar usando las queries de abajo y asi ver como se crean los nodos y relaciones

## Queries utiles

### IMPORTAR
```
LOAD CSV WITH HEADERS FROM "file:///article.csv" AS row
CREATE (n:Article)
SET n = row
```
```
LOAD CSV WITH HEADERS FROM "file:///media.csv" AS row
CREATE (n:Media)
SET n = row
```
```
LOAD CSV WITH HEADERS FROM "file:///tag.csv" AS row
CREATE (n:Tag)
SET n = row
```

### CREAR INDICES
```
CREATE INDEX ON :Article(articleId)
```
```
CREATE INDEX ON :Media(mediaId)
```
```
CREATE INDEX ON :Tag(machineName)
```

### BORRAR NODO Y RELACIONES DEL MISMO
```
MATCH (a:Article) where ID(a)=393
OPTIONAL MATCH (a)-[r]-() //drops a's relations
DELETE r,a
```

### BORRAR RELACIONES DE UN TIPO
```
MATCH ()-[r:RELEASED]-() 
DELETE r
```

### CREAR RELACIONES ENTRE NODOS
```
MATCH (a:Article), (m:Media)
WHERE a.articleId = "TN-912553"
AND m.mediaId = "TN-image-1246155"
CREATE (a)-[:CONTAINS]->(m)
```

### CREAR RELACIONES DESDE ARCHIVO
```
LOAD CSV WITH HEADERS FROM "file:///article-media.csv" AS row
MATCH (a:Article), (m:Media)
WHERE a.articleId = row.articleId
AND m.mediaId = row.mediaId
CREATE (a)-[:CONTAINS]->(m)
```
```
LOAD CSV WITH HEADERS FROM "file:///tag-article.csv" AS row
MATCH (a:Article), (t:Tag)
WHERE a.articleId = row.articleId
AND t.machineName = row.machineName
CREATE (t)-[:TAGGED]->(a)
```

### TRAE LAS IMAGENES DE ARTICULOS QUE TENGAN DETERMINADO TAG
```
MATCH (t:Tag)-->(:Article)-->(m:Media)
RETURN t.machineName as Tag, collect(distinct m.value) as Images
```

### TRAE TODOS LOS ARTICULOS Y SUS TAGS
```
MATCH (a:Article)-[:TAGGED]-(tags)  RETURN a, tags
```

### TRAE UN ARTICULO PARTICULAR Y SUS TAGS
```
MATCH (a:Article)-[:TAGGED]-(tags)
WHERE a.articleId = "TN-912537"
RETURN a, tags
```

### TRAE OTRO ARTICULO RECOMENDADO (de lo que tiene taggeado mi article, buscá que otros articles estan tagged con esos tags)
```
MATCH (a:Article)-[:TAGGED]-()-[:TAGGED]-(recommendedArticle)
WHERE a.articleId = "TN-912537"
RETURN DISTINCT recommendedArticle
```
