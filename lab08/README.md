
# Lab08 - Modelo Lógico e Análise de Dados em Grafos

# Equipe `Analistas de Cardápios`

# Subgrupo `A`
* `Pedro Henrique Antoine Cortez Daccache - 251572`
* `André Rodrigues Alves da Silva - 231392`
* `Gabryel Rodrigues Alves da Silva - 235394`

## Modelo Lógico do Banco de Dados de Grafos
![Modelo Lógico de Grafos](images/modelo-logico-grafos.png)

## Perguntas de Pesquisa/Análise Combinadas e Respectivas Análises

### Pergunta/Análise 1
* Quais são as receitas mais similares, considerando como critério de similaridade a quantidade de alimentos em comum que essas receitas usam?

   * A análise feita para responder essa pergunta corresponde a uma projeção para ligar receitas com receitas. No modelo lógico do banco, essas arestas correspondem ao rótulo "similar a", e possibilitam também mensurar a similaridade através do peso "vezes" na aresta. Para a análise, o peso da aresta é o número de ingredientes em comum entre duas receitas. Feito isso, é possível fazer uma análise de predição de links para prever novas relações a serem feitas quando uma nova receita é adicionada. Essas previsões, nesse contexto, indicariam a qual receita uma receita nova é potencialmente similar, o que possibilita prever novos tipos de receita apenas analisando a estrutura da rede.

### Pergunta/Análise 2
* É possível criar grupos para os alimentos a partir das receitas em que são usados juntos?

   * A análise feita para responder essa pergunta corresponde à projeção que relaciona alimentos com alimentos. No modelo lógico, isso corresponde às arestas de rótulo "complementa", que também têm um peso "vezes". Para essa projeção, podemos relacionar alimentos se eles estiverem juntos. O peso da aresta é o número de receitas em comum. Com isso, a análise a ser feita é uma análise de comunidade. Analisando a estrutura do grafo e as arestas existentes, isso possibilita separar os alimentos em diferentes grupos com base em suas similaridades em receitas, permitindo verificar alimentos que comumente estão em receitas similares e alimentos que não estão. Nesse caso, tomando a análise para o mundo real, alimentos nunca usados em conjunto podem ter sabores não complementares e, portanto, o uso deles juntos em uma mesma receita deve ser evitado.

### Pergunta/Análise 3
* É possível rankear as pessoas de alguma forma com base na dieta delas?

   * A análise feita para responder essa pergunta corresponde às arestas de rótulo "inclui". Para a projeção que cria essas arestas, podemos criar uma aresta de uma pessoa A para uma pessoa B (unidirecional) se a pessoa B consumiu pelo menos 60% dos alimentos que a pessoa A consumiu. Com uma análise de centralidade utilizando page rank, é possível determinar um peso diferente para cada pessoa. Nesse caso, pessoas com peso mais alto são pessoas que consumiram grande parte dos alimentos que outras consumiram. Uma implicação disso seria, por exemplo, de que pessoas com rank muito alto consomem mais do que o necessário - já que a sua dieta inclui a dieta de várias outras pessoas para um mesmo período.

### Pergunta/Análise 4
* Existe alguma relação entre o método de cozimento da receita e a forma dos alimentos constituintes?

   * A análise feita para responder essa pergunta é mostrada nas arestas com rótulo "combinam-se", entre métodos de cozimento e a forma das receitas. Se um alimento foi usado em uma receita de método de cozimento X na forma Y, uma aresta entre os nós X e Y deve ser criada (ou seu peso deve ser aumentado, caso já exista). Depois disso, uma análise de comunidade pode ser feita nesses nós. Apesar de o grafo não ser homogêneo (há nós de rótulos diferentes), podemos simplificar a análise das comunidades apenas importando todos os nós como se fossem de uma mesma categoria e, com isso, analisando se há a formação de comunidades. Caso haja a formação clara de comunidades distintas, isso indicaria que a relação entre método e forma existe. Caso fosse possível observar apenas uma única comunidade, isso indicaria que provavelmente essas métricas não estão relacionadas.
