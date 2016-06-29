# Anotações para 70-461
Anotações para certificação 70-461
# Chapter1

Lesson 1: Understanding the Foundations of T-SQL
====
## Able to:
- Describe the foundations that T-SQL is based on.
- Describe the importance of using T-SQL in a relational way.
- Use correct terminology when describing T-SQL related elements.

T-SQL is based on mathematical foundations, *_Set Theory_*, *_Relational Model_* and *_Predicate Logic_*.
T-SQL is a dialect of standard SQL. SQL is standard o both ISO and ANSI.
Writing in standard way is considered a best pratice. You should consider a nonstandard option only when it has some important benefit to you that is not covered by the standard alternative.

A common misconception is that the name **relational** has to do with relationships between tables. The source for the name is the mathematical concept *relation*.
A *relation* has a heading and body.

**Heading** is a set of attributes (what SQL attempts to represent with collumns). Attribute is identified by name and type name.

**Body** is s set of tuples (what SQL attempts to represent with rows). Each tuple's heading is the heading of relation. Each value of each tuple's attribute is of its respective type
The heading of a relation is a set of attributes, and the body a set of tuples.

> By a “set” we mean any collection M into a whole of de nite, distinct objects m (which are called the “elements” of M) of our perception or of our thought.

A set should be considered as a whole. This means that you do not interact with the individual elements of the set, rather with the set as a whole.

Notice the term distinct, a set has no duplicates.

**Predicate** is an expression that when attributed to some object, makes a proposition either true or false.

## Using Correct Terminology

**Null** is a mark for a missing value

Lesson 2: Understanding Logical Query Processing
====

T-SQL tem um lado fisíco e lógico. O lado **lógico** trata da interpretação conceitual da query, isto explica qual é o resultado correto da query.
O lado **fisíco** é o processamento da query pelo mecanismo de banco de dados. O processamento fisíco deve produzir os resultados definidos pelo processamento lógico.
Para atingir este objetivo o mecanismo do banco de dados pode aplicar otimizações. Otimizações podem reorganizar os passos da lógica de consulta (logical query) ou remover etapas.
Esta reorganização pode acontecer somente se o **resultado** continua o mesmo pelo definido na consulta logica (logical query)

## Able to:
- Understand the reasoning for the design of T-SQL.
- Describe the main logical query processing phases.
- Explain the reasons for some of the restrictions in T-SQL.

SQL standard descreve a iterpretação lógica da requisição declaratica (declarative request) o *"que"*.
Mas é o mecanismo de banco de dados que deve descobrir como processar fisícamente a solicitação o *"como"*

## Logical Query Processing Phases

Principais cláusulas de consulta especificadas na ordem em que você deve digitá-las

1. SELECT
2. FROM
3. WHERE
4. GROUP BY
5. HAVING
6. ORDER BY

Query processing order

1. FROM
2. WHERE
3. GROUP BY
4. HAVING
5. SELECT
6. ORDER BY

Cada fase opera em uma ou mais tabelas como entradas e retorna uma tabela virtual como saída.
A tabela de saída de uma fase é considerada a entrada para a próxima fase.

```sql
SELECT country, YEAR(hiredate) AS yearhired, COUNT(*) AS numemployees
FROM HR.Employees
WHERE hiredate >= '20030101'
GROUP BY country, YEAR(hiredate)
HAVING COUNT(*) > 1
ORDER BY country , yearhired DESC;
```

### 1. Evaluate the FROM Clause
Nesta fase são indicadas as tabelas para consulta e operadores como *joins*. Neste caso o *input* é uma tabela (HR.Employees).
O *output* desta fase é uma tabela resultado com todas a *rows* da tabela de *input*.

```
empid  hiredate    country
------ ----------- --------
1      2002-05-01   USA
2      2002-08-14   USA
3      2002-04-01   USA
4      2003-05-03   USA
5      2003-10-17   UK
6      2003-10-17   UK
7      2004-01-02   UK
8      2004-03-05   USA
9      2004-11-15   UK
```

### 2. Filter Rows Based on the WHERE Clause
A segunda fase filtra as *rows* com base em um predicado na cláusula WHERE. Somente as *rows* para
Qual o predicado for avaliada como **TRUE** são retornadas.

the WHERE filtering phase filters only rows for employees hired on or after
January 1, 2003. Six rows are returned from this phase and are provided as input to the next
one. Here’s the result of this phase.

```
empid  hiredate    country
------ ----------- --------
4      2003-05-03  USA
5      2003-10-17  UK
6      2003-10-17  UK
7      2004-01-02  UK
8      2004-03-05  USA
9      2004-11-15  UK
```
### 3. Group Rows Based on the GROUP BY Clause

Esta fase define um grupo para cada combinação distinta de valores nos elementos agrupados da tabela de entrada.
Em seguida, ele associa cada linha de entrada para o seu respectivo grupo.

```
group    group          detail detail  detail
country  YEAR(hiredate) empid  country hiredate
-------- -------------- ------ ------- ----------
UK       2003           5      UK      2003-10-17
                        6      UK      2003-10-17
UK       2004           7      UK      2004-01-02
                        9      UK      2004-11-15
USA      2003           4      USA     2003-05-03
USA      2004           8      USA     2004-03-05
```


### 4. Filter Rows Based on the HAVING Clause
Esta fase também é responsável pela filtragem de dados com base em um predicado,
mas é avaliada depois que os dados tem sido agrupados,
por isso ele é avaliado por grupo e filtros de grupos como um todo.

Apenas os grupos para os quais o predicado é avaliado como verdadeiro são devolvidos a partir desta fase.
Significando: Filtre somente grupos ***country*** e ***hire year*** que possuem mais de um empregado.

```sql
HAVING COUNT(*) > 1
```
Somente groups UK, 2003 and UK, 2004

```
group    group          detail detail  detail
country  YEAR(hiredate) empid  country hiredate
-------- -------------- ------ ------- ----------
UK       2003           5      UK      2003-10-17
                        6      UK      2003-10-17
UK       2004           7      UK      2004-01-02
                        9      UK      2004-11-15
```

>Quick Check
>- What is the difference between the WHERE and HAVING clauses?
>
>Quick Check Answer
>
>- The WHERE clause is evaluated before rows are grouped, and therefore is evaluated
>per row. The HAVING clause is evaluated after rows are grouped, and therefore
>is evaluated per group.


### 5. Process the SELECT Clause
Esta é a ultima cláusula a ser avaliada no processamento de consultas lógicas.

Primeiro são avaliadas as expressões na lista **SELECT** e produzidos os atributos de resultado.
Isto inclui a atribuição de atributos com nomes se eles são derivados a partir de expressões.   
Se for uma *Grouped query* cada grupo será representado por uma *single row* no resultado. Na consulta atual somente dois grupos permanecem depois de todos os processamentos.   

Segundo passo, aplicado se usada a cláusula ***DISTINCT***, neste caso são removidos os resultados duplicados.   
O quinto processo retorna um ***relational result***, a ordem das rows não é garantida.

Esta fase também atribui aliases para as colunas, sendo assim,
os aliases não estão visiveis para as cláusulas processadas nas fases anteirores *FROM, WHERE, GROUP BY e HAVING*

>Quick Check   
>1. Why are you not allowed to refer to a column alias defined by the SELECT
>clause in the WHERE clause?
>2. Why are you not allowed to refer to a column alias defined by the SELECT
>clause in the same SELECT clause?   
>
>Quick Check Answer   
>1. Because the WHERE clause is logically evaluated in a phase earlier to the one
>that evaluates the SELECT clause.
>2. Because all expressions that appear in the same logical query processing phase
>are evaluated conceptually at the same point in time.

### 6. Handle Presentation Ordering
A sexta fase é aplicada a query possui um cláusula ***ORDER BY***. Esta fase retorna o resultado em uma
ordem de apresentação especifica, de acordo com a expressão que aparece na lista ***ORDER BY***.   
A query de exemplo indica que as result rows devem ser ordenadas primeiro por *country* (in ascending order by default),
e depois por *numemployees* (descending)

```
country  yearhired  numemployees
-------- ---------- ------------
UK       2004       2
UK       2003       2
```

# Chapter 2 Getting Started with the SELECT Statement

## Exam objectives:
1. Work with Data
  - Query data by using SELECT statements.
  - Implement data types.
2. Modify Data
  - Work with functions.

## Lesson 1: Using the FROM and SELECT Clauses


## The FROM Clause
É a primeira cláusula a ser avaliada lógicamente em uma SELECT query e nela podemos:
- Indicar a tabela em que a query será executada.
- Aplicar operadores JOINS

Na cláusula *FROM* também podemos atribuir *alias* para as tabelas das seguintes formas:
```sql
<table> <alias>
ou
<table> AS <alias>
```

Quando atribuimos um *alias* para uma tabela estamos renomeando a tabela dentro do escopo atual da query,
isto torna o nome original da tabela inacessível dentro do escopo da query atual.

## The SELECT Clause
Esta cláusula tem duas funções principais:
- Avaliar as expressões que definem os atributos no resultado da query, dando-lhes *aliases* se necessário.
- Usar a cláusula DISTINCT para para eliminar *rows* duplicadas, se necessário.

```sql
SELECT empid, firstname, lastname
FROM HR.Employees;
```

A cláusula **FROM** indica a *input table* da query. A cláusula **SELECT** projeta apenas três dos atributos
da *input table* como os atributos que devem ser retornados para o resultado da query.

 ## Lesson 2: Working with Data Types and Built-in Functions

Data types restrigem os dados que são suportados e também encapsulam comportamentos que podem operar sobre estes dados, também expoem estes dados atráves de operadores e outros meios.

T-SQL suporta muitas funções embutidas que podem ser utilizadas para manipular dados. Porque as funções operam em valores de entrada e retornar valores de saída, uma compreensão de funções embutidas vai de mãos dadas com uma compreensão dos tipos de dados.

After this lesson, you will be able to:
- Choose the appropriate data type.
- Choose a type for your keys.
- Work with date and time, in addition to character data.
- Work with the CASE expression and related functions.
