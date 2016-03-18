# Java Stream Extensions

One of the best features coming out of [Java 1.8](http://docs.oracle.com/javase/8/docs/api/) has to be its new Streaming framework coupled with the lamda expressions. These go a long way toward making Java a more functional language, although it's still
not [Scala](http://www.scala-lang.org).

Streams allow the developer to chain together operations affecting the entire bulk of elements from some collection or other source without having to explicitly write the procedural statements of iterating through the items in each list. This is a key feature as it allows the Java compiler and JVM to determine the best strategy for iterating through items using multi threads or multiple processors or whatever, and taking it out of the hands of the developer.  Plus the code is prettier, cleaner and cooler.

The following is an  example taken from the [Stream JavaDocs](http://docs.oracle.com/javase/8/docs/api/java/util/stream/package-summary.html) showing the various types of pass-through methods of a stream.
```
int sum = widgets.stream()
          .filter(b -> b.getColor() == RED)
          .limit(10)
          .mapToInt(b -> b.getWeight())
          .sum();
```
Here `filter` is a so-called intermediate method which takes this stream and returns another stream, filtering elements according to its parameter, a  [Predicate](http://docs.oracle.com/javase/8/docs/api/java/util/function/Predicate.html) taking an element from the stream and returning `true` if the item should be passed downsteam or `false` otherwise.

`mapToInt` is another intermediate method, here one that maps stream values to a different type of object, pushing the new stream with the newly mapped objects downstream. It takes a [Function](http://docs.oracle.com/javase/8/docs/api/java/util/function/Function.html) as a callback to perform this mapping.

`limit(10)` is a 'short-circuiting' intermediate method, which in this case delivers 10 items downstream, after which it terminates like other terminating methods.

`sum` is a terminating method which is at the tail end of the stream, accumulates the state of the incoming stream by pulling all elements, exhausting the stream.

Given all this, when the developer works deep enough within the Stream framework she will be confronted with certain limitations.

Suppose I was writing a scheduling application and want to schedule meetings on the first Friday of every month.  My design calls for producing an endless stream of dates which define the first Friday of each month, starting from a certain month. 
```
     Stream<LocalDate> endlessFirstsOfMonth = firstOfEachMonthStream(FRIDAY,startDate);
     ...
```
This is an endless stream because:
* it's easier to create an endless stream than a finite stream, actually, and 
* we don't necessarily define an ending date, because we don't know when our meetings will come to an end and the future (famously) is hard to predict.
Clearly, I won't be using the entire infinite array. Instead in my case I want to build just this year's calendar of events, and so I want to limit my stream to just those dates through the end of the year. The problem is that I can't use `limit` because `limit` only takes a count and I don't know and/or am too lazy to count how many items this will be.
What I need is a special `limitWhile` method, a short-circuiting, intermediate method which takes a `Predicate` rather that an `int`, which I can use to handle the stream the right way. Something like this:
```
     Predicate<LocalDate> dateIsBeforeEnd = d -> d.isBefore(endDate);
     List<LocalDate> events = endlessFirstsOfMonth.limitWhile(dateIsBeforeEnd).collect(Collectors.toList());
```
The problem is: `limitWhen` doesn't exist in the `Stream` API, and you can't insert your custom method there.
But we can do the next best thing, and set up a set of functions that take a stream and returns a stream, and break up the above into an inverted statement like this:
```
     List<LocalDate> events = limitWhile(endlessFirstsOfMonth, dateIsBeforeEnd).collect(Collectors.toList());
```



