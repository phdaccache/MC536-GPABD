
# Lab06 - Patologias, Medicamentos e Efeitos Colaterais em Cypher

# Equipe `Analistas de Cardápios`

# Subgrupo `A`
* `Pedro Henrique Antoine Cortez Daccache - 251572`
* `André Rodrigues Alves da Silva - 231392`
* `Gabryel Rodrigues Alves da Silva - 235394`

## Tarefa de Cypher sobre Patologias, Medicamentos e Efeitos Colaterais

## Exercício

Faça a projeção em relação a Patologia, ou seja, conecte patologias que são tratadas pela mesma droga.

### Resolução
Considerando um critério de relevância de que duas patologias são tratadas pela mesma droga se a droga foi utilizada mais de 20 vezes no tratamento:
~~~cypher
MATCH (p1:Pathology)<-[a]-(d:Drug)-[b]->(p2:Pathology)
WHERE a.weight > 20 AND b.weight > 20
MERGE (p1)<-[r:Relates]->(p2)
ON CREATE SET r.weight = 1
ON MATCH SET r.weight = r.weight + 1
~~~
Visualizando:
~~~cypher
MATCH (p1:Pathology)<-[:Relates]->(p2:Pathology)
RETURN p1, p2
LIMIT 20
~~~

# Trabalhando com Efeitos Colaterais

## Exercício

Construa um grafo ligando os medicamentos aos efeitos colaterais (com pesos associados) a partir dos registros das pessoas, ou seja, se uma pessoa usa um medicamento e ela teve um efeito colateral, o medicamento deve ser ligado ao efeito colateral.

### Resolução
Para realizar todos os passos e construir o grafo, primeiro é preciso criar os nós referentes às pessoas, aos medicamentos e aos efeitos colaterais. Os comandos a seguir fazem isso:
~~~cypher
LOAD CSV WITH HEADERS FROM 'https://raw.githubusercontent.com/santanche/lab2learn/master/data/faers-2017/demo.csv' AS line
CREATE (:Person {idperson: line.IdPessoa, age: line.Idade, gender: line.Genero})
~~~
~~~cypher
LOAD CSV WITH HEADERS FROM 'https://raw.githubusercontent.com/santanche/lab2learn/master/data/faers-2017/drug.csv' AS line
CREATE (:Drug {code: line.code, name: line.name})
~~~
~~~cypher
LOAD CSV WITH HEADERS FROM 'https://raw.githubusercontent.com/santanche/lab2learn/master/data/faers-2017/pathology.csv' AS line
CREATE (:Pathology {code: line.code, name: line.name})
~~~
É possível também criar índices para aumentar a velocidade das operações. Considerando os campos mais utilizados,  isso pode ser feito da seguinte forma:
~~~cypher
CREATE INDEX FOR (p:Person) ON (p.idperson)
~~~
~~~cypher
CREATE INDEX FOR (d:Drug) ON (d.code)
~~~
~~~cypher
CREATE INDEX FOR (p:Pathology) ON (p.code)
~~~

Agora, precisamos relacionar pessoas com os medicamentos que elas tomaram:
~~~cypher
LOAD CSV WITH HEADERS FROM 'https://raw.githubusercontent.com/santanche/lab2learn/master/data/faers-2017/drug-use.csv' AS line
MATCH (d:Drug {code: line.codedrug})
MATCH (p:Person {idperson: line.idperson})
CREATE (p)-[:Used]->(d)
~~~

Em seguida, precisamos relacionar pessoas com os efeitos colaterais que elas tiveram:
~~~cypher
LOAD CSV WITH HEADERS FROM 'https://raw.githubusercontent.com/santanche/lab2learn/master/data/faers-2017/sideeffect.csv' AS line
MATCH (p:Person {idperson: line.idPerson})
MATCH (pa:Pathology {code: line.codePathology})
CREATE (p)-[:Experienced]->(pa)
~~~

Por fim, podemos realizar a query que responde a pergunta proposta. As relações entre medicamentos e efeitos colaterais podem ser feitas da seguinte forma. O peso de cada aresta é o número de vezes em que alguém utilizou o medicamento e teve o efeito colateral.
~~~cypher
MATCH (d:Drug)<--(p:Person)-->(pa:Pathology)
MERGE (d)<-[r:Relates]->(pa)
ON CREATE SET r.weight = 1
ON MATCH SET r.weight = r.weight + 1
~~~

Visualizando os mais relevantes
~~~cypher
MATCH (d:Drug)<-[r:Relates]->(p:Pathology)
WHERE r.weight > 50
RETURN d, p
~~~

## Exercício

Que tipo de análise interessante pode ser feita com esse grafo?

Proponha um tipo de análise e escreva uma sentença em Cypher que realize a análise.

### Resolução
Uma análise interessante que pode ser feita a partir do grafo é analisar quantas vezes os efeitos colaterais são observados juntos a partir do uso de um mesmo medicamento. Como critério, podemos considerar que se um medicamento causou mais de 5 vezes um par de efeitos colaterais, eles estão correlacionados para aquele medicamento, ou seja, um "causa o outro".
~~~cypher
MATCH (pa:Pathology)<-[a]-(d:Drug)-[b]->(pb:Pathology)
WHERE a.weight > 5 AND b.weight > 5
MERGE (pa)<-[t:Times]->(pb)
ON CREATE SET t.weight = 1
ON MATCH SET t.weight = t.weight + 1
~~~
O peso de cada aresta entre os efeitos colaterais é o número de medicamentos que causaram ambos. Visualizando os mais relacionados:
~~~cypher
MATCH (pa:Pathology)<-[t:Times]->(pb:Pathology)
WHERE t.weight > 30
RETURN pa, pb
~~~