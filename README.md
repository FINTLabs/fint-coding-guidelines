# FINT coding guidelines

# WORK IN PROGRESS

These guidelines were created to ensure consistency within FINT source code.


## 1. General

### 1.1 Code style

FINT uses the default [Java code style in IntelliJ IDEA](https://www.jetbrains.com/help/idea/code-style-java.html).

1. Always format the code before committing the code into the repository.  
2. If code requires reformatting while you are working on another task, add the format changes to its own commit. This makes it easier to review the code. 


### 1.1 Small methods/classes

_The first rule of functions is that they should be small._
_The second rule of functions is that they should be smaller than that._ **[[1]](#references)**

A class should have a single responsibility. It should have a single reason to change.

Methods should do "one thing". Try to keep all logic in a method on the same level of abstraction.

It is difficult to set a number of lines that is too much for a class/method, but we use these guidelines:
1. Is the method tens of lines long, perhaps it is working on multiple levels of abstraction? 
2. Is the class hundreds of lines long, perhaps the class has more than one responsibilty?
3. Is the class name very generic? The class is most likely dealing with multiple responsibilities.
4. **Do you have a hard time finding a good name for a class? It could be  separate concepts within the same class and that it should be split into two classes.**


### 1.2 Naming

_There are only two hard things in Computer Science: cache invalidation and naming things._ **[[3]](#references)**

_The name of a variable, function, or class, should answer all the big questions. It should tell you why it exists, what it does, and how it is used. If a name requires a comment, then the name does not reveal its intent._ **[[1]](#references)**

Good naming and encapsulation:
```
public class EventState {
    private final long expires;

    public EventState(int timeToLiveMinutes) {
        expires = System.currentTimeMillis() + TimeUnit.MINUTES.toMillis(timeToLiveMinutes);
    }

    public boolean expired() {
        return (System.currentTimeMillis() > expires);
    }
}
```

This class has an internal variable called _expires_. This calculates when the event is expired. It does not expose it, but provides a method called `expired()` instead. This is an easily understandable name, when called it will let you know if the event has expired or not.

_The length of a name should correspond to the size of its scope._ **[[1]](#references)**

If the scope of the name is small we can use a short name:
```
for(int i = 0; i < 10; i++) {
  ...
}
```

If the scope is large we should use a longer name:
```
public static final String CACHE_INITIALDELAY_ARBEIDSFORHOLD = "${fint.consumer.cache.initialDelay.arbeidsforhold:60000}";
```

Consistency is very important, use the same names for the same concept. This will make it easier to read and understand the code.


### 1.3 Java language features

*Streams*
The streams API was added in Java 8 to ease the task of performing bulk operations, sequentially or in parallell. **[[2]](#references)**

A simple example where streams will improve the code:

We have a list of names that we want to uppercase. Using a for-loop it can be done like this:xample
```
List<String> uppercaseNames = new ArrayList<>();
for (String name : names) {
    uppercaseNames.add(name.toUpperCase());
}
```

By using streams we can improve the code:
```
List<String> uppercaseNames = names.stream()
  .map(name -> name.toUpperCase())
  .collect(Collectors.toList());
```

*Method references*
Method references usually result in shorter, clearer code. **[[2]](#references)**

And we can further enhance the code in this example by using method references:
```
names.stream()
  .map(String::toUpperCase)
  .collect(Collectors.toList());
```

While the use of streams and method references are recommended, there are places where they make the code less readable.

Example from [Functional Programming Patterns With Java 8](https://dzone.com/articles/functional-programming-patterns-with-java-8)
```
// DON'T DO THIS
public List<Product> getFrequentOrderedProducts(List<Order> orders) {
        return orders.stream()
                        .filter(o -> o.getCreationDate().isAfter(LocalDate.now().minusYears(1)))
                        .flatMap(o -> o.getOrderLines().stream())
                        .collect(groupingBy(OrderLine::getProduct, summingInt(OrderLine::getItemCount)))
                        .entrySet()
                        .stream()
                        .filter(e -> e.getValue() >= 10)
                        .map(Entry::getKey)
                        .filter(p -> !p.isDeleted())
                        .filter(p -> !productRepo.getHiddenProductIds().contains(p.getId()))
                        .collect(toList());
}
```

This code is hard to read. Splitting it up into smaller methods will greatly help with the readability.

When used appropriately, streams can make programs shorter and clearer; when used inappropriately, they can make programs difficult to read and maintain. **[[2]](#references)**


### 1.4 Comments
 
_Comments are, at best, a necessary evil._ **[[1]](#references)**

```
// Don't do this!!
public enum Status {
    NOT_QUEUED, // this test is created, but not even queued
    RUNNING, // this test is still running
    ...
```

This code represents a statuses. These comments are not needed. They do not provide any extra information, they are just adding noise to the code. Do not write comments like the example above.

1. Prefer good names rather than having to add a comment.
2. Avoid commenting out code. Do not commit the commented out code into the source code repository. If it is not needed any more, delete it.

While the general guideline is to avoid the use of comments, there are situations where comments can be useful:
1. Public APIs. If you are creating a library, add javadoc comments to the classes that will be used by other projects.
2. Usage of a third party library. There can be situations where the use of a library is not self explanatory. If the code is getting complex due to code you have no control over, write a comment explaining why it is implemented this way.


## 2. Testing

We also consider the tests to provide useful documentation for how the code works.

### 2.1 Test naming

### 2.2 Asserts

### 2.3 Mocking

# References

1. [Robert C. Martin - Clean Code: A Handbook of Agile Software Craftsmanship](https://www.amazon.com/Clean-Code-Handbook-Software-Craftsmanship/dp/0132350882)
2. [Joshua Bloch - Effective Java (3rd Edition)](https://www.amazon.com/Effective-Java-3rd-Joshua-Bloch/dp/0134685997)
3. [Phil Karlton](https://martinfowler.com/bliki/TwoHardThings.html)