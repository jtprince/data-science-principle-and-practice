<!-- $theme: gaia -->
<!-- $size: 16:9 -->

How do DB Indexes work?
=======================

---

# A database

Conceptually, consecutive records on disk.

    [Doe, John, 4111 Dover Ln, jtprince@gmail.com]
    [Doe, Jane, 33 Whitenose Ave, jane@gmail.com]
    [Bob, Billy, 8114 Tailbrush Way, bb@gmail.com]

---

# Records are addressable

    <add1>[Doe, John, 4111 Dover Ln, jtprince@gmail.com]
    <add2>[Rogers, Jane, 33 Whitenose Ave, jane@gmail.com]
    <add3>[Bob, Billy, 8114 Tailbrush Way, bb@gmail.com]

---

# DB indexes are SORTED

A DB index consists of sorted rows pointing from field -> address

```
pk1 -> add1
pk2 -> add2
pk3 -> add3
```

---

# Can create index on any field

Make a last name index

```
Bob    -> add3
Doe    -> add1
Rogers -> add2
```

---

# Finding an index is at most big O of log2

![150%](https://upload.wikimedia.org/wikipedia/commons/8/83/Binary_Search_Depiction.svg)


---

# Store as a B-Tree in Mysql

* Self balancing (keeps things optimally fast for retrieval)
* Variable number of child nodes (stays flat and better disk retrieval in chunks)
* Fast insertion/deletion (great for a DB!)

![B-Tree](https://www.geeksforgeeks.org/wp-content/uploads/BTreeIntro1.png)

\*\* - Nobody knows what the 'B' stands for, but I say 'balancing'!

---

# B-Trees allow fast insertion/deletion

[B-Tree: fast insertion and deletion](https://www.youtube.com/watch?v=coRJrcIYbF4)


---

# Specifically, a B+ Tree

B+ Trees store all data in leaf nodes.

![all data in leaf nodes](http://www.vertabelo.com/_file/blog/all-about-indexes-part-2-mysql-index-structure-and-performance/b-plus-tree.png)

(the simpler structure just works better for most DBs)
