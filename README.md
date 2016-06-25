#Anotações para 70-461
Anotações para certificação 70-461
#Chapter1

Lesson 1: Understanding the Foundations of T-SQL
====
##Able to:
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

##Using Correct Terminology

**Null** is a mark for a missing value

