# Java Stream Extensions

One of the best features coming out of [Java 1.8](http://docs.oracle.com/javase/8/docs/api/) has to be its new Streaming framework coupled with the lamda expressions. These go a long way toward making Java a more functional language, although it's still
not [Scala](http://www.scala-lang.org).

Streams allow the developer to chain together operations affecting the entire bulk of elements from some collection or other source without having to include the procedural steps of iterating through each item. This is a key feature as it allows the Java compiler and JVM to determine the best strategy for iterating through items and takes it out of the hands of the developer.  Plus the code is prettier and cleaner.

The following is an  example taken from the [Stream JavaDocs](http://docs.oracle.com/javase/8/docs/api/java/util/stream/package-summary.html) showing the various types of pass-through methods of stream.
```
int sum = widgets.stream()
          .filter(b -> b.getColor() == RED)
          .mapToInt(b -> b.getWeight())
          .sum();
```

`filter` is a so-called intermediate method which takes this stream and returns another stream, filtering elements according to its parameter, a  [Predicate](http://docs.oracle.com/javase/8/docs/api/java/util/function/Predicate.html) taking an element from the stream and returning `true` if the item should be passed downsteam or `false` otherwise.

`mapToInt` is another intermediate method, but one that maps stream values to a different type of object, pushing the new stream with the newly mapped objects downstream.

`sum` is a terminating method which is at the tail end of the stream, accumulates the state of the incoming stream by pulling all elements, exhausting the stream.

Given all this, when the developer works deep enough within the Stream framework she will be confronted with certain limitations. 
