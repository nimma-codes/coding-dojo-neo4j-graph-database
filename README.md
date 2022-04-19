# Infi Dojo Neo4j for beginners

## What is Neo4j?

Neo4j is a native graph database. It's a No-SQL database which focuses on storing and querying complex relations. It stores the relationships between _nodes_ (objects) on each of these objects. This way Neo4j doesn't have to lookup relationships at query time. This makes it faster to query relations compared to e.g. a SQL-database, which needs to do (indexed) lookups over and over at query time.

The benefits compared to other databases are more noticeable when the data and query complexity increase.

**TODO a small example problem here?**

You can find more detailed information at the Neo4j website: https://neo4j.com/developer/graph-database.

### What will you learn in this dojo?

- You'll learn about basic concepts, such as _nodes_, _Labels_ and _relationships_.
- We'll do some simple queries to get you started with _Cypher_, the query language Neo4j uses.
- After this basic introduction you'll can chose one of the following paths:
  - OR: Build an application in node/C# (**TODO**) with neo4j
  - OR: Dive in the world of algorithms to find more complex relations

### What do we expect from you?

Not much!
explaination

- But at least a basic understanding about databases.
- Make sure you've a Neo4j account so you can use the free [sandbox environment](https://sandbox.neo4j.com/)
- If you want to get started with building a node/C# you'll need an environment (**TODO**).
- We'd also love to hear your opinion on the subject during this evening.

### What can you expect from us?

- For starters pizza, beer and other drinks.
- A casual environment where learning and programming is a core theme;
- We're by no means experts on the subject, but we've done some research in order to give you this dojo. We'd like to share our thoughts and experiences with you.

## Chapter one: Basic concepts and queries

### Neo4j's basic components

Graph databases have no concepts of tables, records or foreign keys. Instead labels, nodes and relationships are uses which resemble these concepts somewhat. Let's take a look at them:

**Nodes**

Nodes are the entities in the graph.

- Nodes can be tagged with _labels_, representing their different roles in your domain. (For example, Person).
- Nodes can hold any number of key-value pairs, or properties. (For example, name)
- Node _labels_ may also attach metadata (such as index or constraint information) to certain nodes.

**Relationships**

- Relationships provide directed, named, connections between two node entities (e.g. Person LOVES Person).
- Relationships always have a direction, a type, a start node, and an end node, and they can have properties, just like nodes.
- Nodes can have any number or type of relationships without sacrificing performance.
- Although relationships are always directed, they can be navigated efficiently in any direction.

### The query language: Cypher

The Cypher query language is quite intuitive, as it visually resembles the components of your query.

Nodes are defined by parenthesis `()` and relationships by arrows `-->`

These can get a little more complex later on, but you'll always recognize these two based on their form in the query.

**A simple query**

A very simple query could look like this:

```
MATCH (n1)-->(n2)
RETURN n1, n2
```

This will return all nodes that have a relationship which each other. Fun, but not very useful.

**Query by property**

In the real world you'd probably want to specify things a little bit more.

```
MATCH (n1{id: 7})-->(n2)
Return n2
```

This will return all related nodes for the node with id 7. You can limit your query based on every property or properties a node has. e.g.:

```
MATCH (n1{age: 7, city: Amsterdam})-->(n2)
Return n2
```

**Query by label**

Besides properties, nodes can have labels. These are a bit similar to table names in a sql database. We can use them categorize the different entities in our database. For instance:

```
MATCH (p:Person)-->(b:Book)
RETURN p,b
```

This will return all relationships between persons and books.

**Query by relationship**

We know how to query relationships between certain object now. But we've no clue what the nature of this relationship is. Fortunately we can also specify the type of relationship in our query.

```
MATCH (p:Person)-[:LOVES]->(m:Movie)
RETURN p,m
```

OR

```
MATCH (p:Person)-[:HATES]->(m:Movie)
RETURN p,m
```

**Summary**

All this new syntax might be a bit overwhelming. But it's just syntax, so don't worry! Here's a neat little picture which summerizes the basic elements of cypher query. _Make sure you understand all aspects before you continue_ and feel free to ask us for extra explanation.
![cypher-example](cypher-example.png)

## Try it yourself

Let's get our hands dirty now! Open a new movie db sandbox on [sandbox environment](https://sandbox.neo4j.com/).

This is a simple graph database filled with some example data to get us started.

### 1. How many are there?
Let's explore this database a bit. 

**How many movies?**
Let's find out how many movies there are. We'll use the count function:

```
MATCH (movies:Movie)  
RETURN count(movies)
```

**How many actors?**

That was easy!
Now we'd like to find out how many actors there are. So we need to expand our MATCH statement:
```
MATCH (actors:Person)-[:ACTED_IN]->(:Movie)
```

Nice! Now we know there are 102 actors!
Or... wait. Did you get 172?

The sandbox graph ui hides the fact that you query every `ACTED_IN` relation for every actor and movie combination. To prove this run the following query and open the text view of the query result (on the left side).

```
MATCH (actors:Person)-[:ACTED_IN]->(movies:Movie)  
RETURN actors, movies
```

Every relation is queried, and thus counted! Just like a `JOIN` in SQL.

The fix is easy, use the DISTINCT keyword: `RETURN count(DISTINCT actors)`. Now it should return 102.

**Keep on going!**

Can you find out how many directors, writers and reviewers there are? 

Tip: you can query all relationship types like this:  

```
MATCH ()-[r]->()  
RETURN DISTINCT type( r)
```

### 2. Who acted in The Matrix?

Can you query all the actors that played in The Matrix?

Tip: use the title property on the Movie nodes. When you query all movies and hover over them you'll see the properties at the bottom of the query result.

If you succeeded you might want to find out which other people were involved with The Matrix.

Tip: instead of returning specific variables, you can also use a wildcard: `RETURN *`

### 3. Who's Eddie?

This one is a bit tricky!

Please find out which actor played Eddie and in which movie.

Note that relationships can have properties as well, just as nodes. In this case we need to lookup the roles property (Which is an array by the way! Who = `['Eddie']`?)

### 4. Act like you know how(ard)

Can you find out which actors played in movies directed by Ron Howard?

Tip: `()-[]->()<-[]-()`

### 5. CrUD
We've matched (read) some things now. But we'd like to give you a quick introduction in how you can create, update and delete entities in Neo4j.

**Create all the things**
Add the movie "Django Unchained" to the database. Instead of the `MATCH` keyword, you need to use the `CREATE` keyword. Don't return anything.

You can also create relationships with the same syntax as you've used with `MATCH`.
Let's add "Quentin Tarantino" as the director. Mind the direction of the relationship. In this database the relations go from persons to movies. Try it!

You can create multiple nodes and relationships with the `CREATE` keyword. But you'll get an error if you try to create existing nodes. In this case we need to `MATCH` our movie first and create the director and relationship on that matched movie. Try it!

This can be cumbersome at times, in which case the `MERGE` statement is a good replacement for `CREATE`. It functions as a upsert method, meaning it'll create non existing entities an matches existing entities. Please add the actor Jamie Fox to this movie using the `MERGE` statement only.

**Update entities**
TODO

## Chapter Two: Recommendations

### 1. Add new reviewers

First find out what properties reviewers have. Note that reviewers are of type Person.

Write a query that:

- Creates 20 new reviewers with a unique name and has 40% chance to create a random rating from each reviewer to a movie. Make sure to add yourself as a reviewer.

  Tip: use `UNWIND` to loop over an array of names `["John Doe", "Jane Doe"]`

  and use `FOREACH(ignoreMe IN CASE WHEN {{your condition}} THEN [1] ELSE [] END | {{your query here}})`

- Has 25% chance to create a :FOLLOWS relationship from each reviewer to another reviewer
- Adds a born property with a random birth year to each reviewer

### 2. Reviewer recommendations: second-degree contacts

Can you write a query that recommends 10 reviewers that are being followed by reviewers that you are following? These are reviewers you might know or might want to get to know. (Your name should be in the database after completing the previous assignment)

Save this query somewhere for later

### 3. Reviewer recommendations: similar movie ratings

Find out which reviewer gives the most similar movie ratings to you. Please return the first 10 results for this one too. Save this query somewhere for later

If you want to give some weight to the count of reviewed movies you could roll your own averaging function using `sum()` and `count()`. Otherwise, just use `avg()`

### 4. Reviewer recommendations: similar age

Since you completed the other recommendation queries this one shouldn't be too hard. Let's find the first 10 reviewers with an age closest to you.

Tip: save this query somewhere for later

### 5. Movie recommendations: movies that reviewers around your age like

From recommending reviewers to recommending movies. Please find the ten best rated movies for the reviewers that were born closest to your birth year.

### 6. Movie recommendations: best rated movie for the genre you like the best

First add Genre nodes with an `IN_GENRE` relationship to all movie nodes based on the mapping you can find [here](/movie-genres.json).

Run

```
CALL apoc.load.json("https://raw.githubusercontent.com/infi-nl/dojo-neo4j-graph-database/feature/recommendations/movie-genres.json?token=GHSAT0AAAAAABPKD3ICS35FT2FSYE7AWPPUYS37MSQ")
YIELD value
MATCH (m:Movie {title: value.title})
UNWIND value.genre as genre
MERGE (g:Genre {name: genre})
MERGE (m)-[:IN_GENRE]->(g)
RETURN m,g
```

Now write a query that returns the 10 best rated movies for the genre you like best. Like in exercise 3 you can use `avg()` or write an average function yourself.

### 7. Recommendations: Pearson algorithm

Let's work with another dataset that enables us to use the Graph Data Science library. Open a new [sandbox](https://sandbox.neo4j.com/) named "Recommendations". It's similar to the movie dataset we have been using.

Now try to find users (nodeLabel: `Users`) most similar to "Cynthia Freeman" based on their movie ratings (relationshipType: `RATED`). But this time use the [`gds.similarity.pearson` algorithm](https://neo4j.com/docs/graph-data-science/current/algorithms/similarity-functions/). This algorithm is particularly well-suited for product recommendations because it takes into account the fact that different users will have different mean ratings: on average some users will tend to give higher ratings than others. Since Pearson similarity considers differences about the mean, this metric will account for these discrepancies.

### 8. Recommendations: Similar movies based on multiple criteria

Now let's find the 10 most similar movies to "Matrix, The" based on budget, imdbRating, revenue and release year.

First you'll have to use the Graph Data Science library to normalize these properties. Otherwise one property could have more weight in the similarity algorithm. Run the following queries sequentially to normalize the properties.

```
CALL gds.graph.project("movieGraph", [{Movie: {properties: ["budget", "imdbRating", "revenue", "year"]}}], "*")
```

```
CALL gds.alpha.scaleProperties.mutate("movieGraph", {nodeProperties: ["budget", "imdbRating", "revenue", "year"], scaler: "MinMax", mutateProperty: "scaledProperties"})
YIELD nodePropertiesWritten
```

```
CALL gds.graph.writeNodeProperties("movieGraph", ["scaledProperties"])
```

Note that you can change the scaler and choose one depending on the distribution of your data.
If all went well you will have created a scaledProperties property on the Movie node. Go and see what it looks like.

Now you can actually write the query. Use the [gds.similarity.euclideanDistance algorithm](https://neo4j.com/docs/graph-data-science/current/algorithms/similarity-functions/) to find the 10 most similar movies to "Matrix, The" based on budget, imdbRating, revenue and release year.
