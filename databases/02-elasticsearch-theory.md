<!-- $size: 16:9 -->

Lucene: How does it work?
================================

---

# Questions

* What motivated the creation of Lucene?
* What's an inverted index?
* How to achieve super fast search?
* How to fit all that data?

---

# Lucene: Indexing Text Documents

Want to be able to retrieve a document based on one or more words.

    doc1: "Hangin' with my friends on Friday ..."
    doc2: "Friends, Romans, countrymen ..."
    doc3: "Fourscore and seven years ago ..."
    ...

---

# A Forward Index

Easy to make!

    doc1: ["hangin", "with", "my", "friends", "on", "Friday"]
    doc2: ["friends", "romans", "countrymen" ..."]
    doc3: ["fourscore", "seven", "years", "ago" ..."]

To search for the documents containing a particular word, you would need to traverse each document's index.  This would take forever, especially with lots of documents.

---

# Inverted Index

    doc1: ["hangin", "with", "my", "friends", "on", "Friday"]
    doc2: ["friends", "romans", "countrymen" ..."]
    doc3: ["fourscore", "seven", "years", "ago" ..."]
    
    ---

    hangin -> [1]
    with -> [1]
    my -> [1]
    friends -> [1, 2]
    on -> [1]
    ...
    
---

# Another example of an inverted index

![80%](https://nlp.stanford.edu/IR-book/html/htmledition/img54.png)

---

# Words and Documnets ("Postings")

    Words      Documents
    -------------------
    hangin	    1
    friends     1, 2
    on          1

* How do you look up words _really_ fast?
* How do you compress postings?

---

# Fast word lookup: Trie

![110%](https://i.stack.imgur.com/i1LRz.png)

* Also allows range type queries!
* Also allows prefix queries!
* The structure can be comopressed for maximum performance

---

# Numbers

Originally stored in lucene as left padded strings!

    "002"
    "017"
    "023"

Can use normal text lookup!

---

# Using a trie for a numeric range query

![160%](https://i1.wp.com/blog.parse.ly/wp-content/uploads/2015/03/trie.png?resize=300%2C215&ssl=1)

---

# Geo spatial data

Lucene now uses a geo-spatial data structure

---

# Binary Space Partition Tree

Like a BPS Tree

![binary space partitioning trees](https://cdn-images-1.medium.com/max/800/1*cBhghbYM3H_yDYdYeWhRQQ.png)

---

# K-D-B Tree

And sort of like a B+ Tree with lots of docs at each node.

![80%](https://cdn-images-1.medium.com/max/800/1*zPM4tGutpn-FvJbeyGHh4A.png)

---

# Bkd Tree

Binary tree at the top, B+ Tree at the bottom

![70%](https://cdn-images-1.medium.com/max/800/1*O4Ari0_Mdvqi6E-MJjFsrQ.gif)

* cache friendly
* low height
* internal nodes never modified

---

# How to store numbers and dates?

### Use a 1D Bkd Tree

Numbers and dates are simply the 1-D version of multi-dimension data.

---

# How to store the "Postings"

What's the most compact representation you can think of?

---

# "Frame of Reference"

* divide the postings into blocks
* transform into deltas
* [note largest delta in block and...]
* compress into smallest space possible

---

# "Frame of Reference"

![120%](https://www.elastic.co/assets/blt42bcd2cef05d6634/for.png?uid=blt42bcd2cef05d6634)

---

# "Roaring bitmaps" (for filter caching)

For frequently executed queries, we need:

* faster retrieval
* maximal compression not quite as important

---

# Filter caching

Filter caching: associating _in memory_ the documents which correspond with a particular filter to minimize disk retrieval.

    date >= 2012-01-04
    sku_id = "TARP13551"
    contains 'purple'

example:

    "contains 'purple'" -> doc 3, doc 7, doc 15

---

# Roaring bitmaps for Filter Caching

``` text
1. Chunk everything

2. If > 4096 values in your block:

      encode as a "bit set" [0, 1, 1, 0, 0, 0 ...]

   Else:

      encode as ints
```

\*\* - really use shorts instead of ints because the chunk allows you to contextualize index number with a notation in the chunk header.

---

# Compressed Bitsets Are Awesome

* Super compact
* Can perform binary operations (e.g. intersection) **without decompression**

---

# Compressed Bitsets are Horrible

* You cannot easily insert/remove records!

---

# Lucene segments

* "words" and postings added every second to new segments
* deleted documents are marked as deleted (won't show in results)
* at least one thread is constantly walking through new segments and non-deleted data from existing segments and merging them into new, larger segments.

---

# Lucene segment merging

![70%](lucene_segment_merging.png)

---

# Lucene segment merging

[Segment merging visualization](https://www.youtube.com/watch?v=YW0bOvLp72E)

---

# B-Tree vs. Trie

DB index vs. Elasticsearch index

* B-Tree is slower because a full comparison must be done at each node.
* B-Tree is easily updatable, trie is not.
* Records are easily updatable, postings are not. 
