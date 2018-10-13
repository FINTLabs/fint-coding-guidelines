# FINT coding guidelines

These guidelines were created to ensure consistency within FINT source code.

---

- [FINT coding guidelines](#fint-coding-guidelines)
  - [General](#general)
    - [Code style](#code-style)
    - [Small methods/classes](#small-methodsclasses)
    - [Naming](#naming)
    - [Java language features](#java-language-features)
    - [Comments](#comments)
  - [Testing](#testing)
    - [Test naming](#test-naming)
    - [Asserts](#asserts)
  - [References](#references)

## General

### Code style

FINT uses the default [Java code style in IntelliJ IDEA](https://www.jetbrains.com/help/idea/code-style-java.html).

1. Always format the code before committing the code into the repository.  
2. If code requires reformatting while you are working on another task, add the format changes to its own commit. This makes it easier to review the code. 


### Small methods/classes

_The first rule of functions is that they should be small._  
_The second rule of functions is that they should be smaller than that._ **[[1]](#references)**

A class should have a single responsibility. It should have a single reason to change.

Methods should do _one thing_. Try to keep all logic in a method on the same level of abstraction.

**It is difficult to set a number of lines that is too much for a class/method, but we use these general guidelines:**
1. Is the method tens of lines long, perhaps it is working on multiple levels of abstraction? 
2. Is the class hundreds of lines long, perhaps the class has more than one responsibilty?
3. Is the class name generic? The class is most likely dealing with multiple responsibilities.
4. **Do you have a hard time finding a good name for a class? It could because you have separate concepts within the same class and that it should be split into two classes.**


### Naming

_There are only two hard things in Computer Science: cache invalidation and naming things._ **[[3]](#references)**

_The name of a variable, function, or class, should answer all the big questions. It should tell you why it **exists, what it does, and how it is used**. If a name requires a comment, then the name does not reveal its intent._ **[[1]](#references)**

Good naming and encapsulation:
```java
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

The _EventState_ class has an internal variable called _expires_. It does not expose it, but provides a method called `expired()`. This calculates when the event is expired.  
It is easy to understand what this method provides and an important concept here is the encapsulation of the internal state. It is not necessary for external components to know the details of how the `expired()` method works.

_The length of a name should correspond to the size of its scope._ **[[1]](#references)**

If the scope of the name is small we can use a short name:
```java
for(int i = 0; i < 10; i++) {
  ...
}
```

If the scope is large we should use a longer name:
```java
public static final String CACHE_INITIALDELAY_ARBEIDSFORHOLD = "${fint.consumer.cache.initialDelay.arbeidsforhold:60000}";
```

Consistency is very important, use the same names for the same concept. This will make it easier to read and understand the code.


### Java language features

**Streams**
_The streams API was added in Java 8 to ease the task of performing bulk operations, sequentially or in parallell._ **[[2]](#references)**

Lets look at a simple example. We have a list of names that we want to uppercase. Using a for-loop it can be done like this:
```java
List<String> uppercaseNames = new ArrayList<>();
for (String name : names) {
    uppercaseNames.add(name.toUpperCase());
}
```

By using streams we can improve the code:
```java
List<String> uppercaseNames = names.stream()
  .map(name -> name.toUpperCase())
  .collect(Collectors.toList());
```

*Method references*
Method references usually result in shorter, clearer code. **[[2]](#references)**

And we can further enhance the code in this example by using method references:
```java
names.stream()
  .map(String::toUpperCase)
  .collect(Collectors.toList());
```

While the use of streams and method references are recommended, there are places where they make the code less readable.
An example of this is the code below, taken from [Functional Programming Patterns With Java 8](https://dzone.com/articles/functional-programming-patterns-with-java-8)
```java
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


### Comments
 
_Comments are, at best, a necessary evil._ **[[1]](#references)**

```java
// DON'T DO THIS
public enum Status {
    NOT_QUEUED, // this test is created, but not even queued
    RUNNING, // this test is still running
    ...
```

This code represents a statuses. These comments are not needed. They do not provide any extra information, they are just adding noise to the code. Do not write comments like the example above.

1. Prefer good names rather than having to add a comment.
2. Avoid commenting out code. Do not commit the commented out code into the source code repository. If it is not needed any more, delete it.

**While the general guideline is to avoid the use of comments, there are situations where comments can be useful:**
1. Public APIs. If you are creating a library, add javadoc comments to the classes that will be used by other projects.
2. Usage of a third party library. There can be situations where the use of a library is not self explanatory. If the your source code is becoming complex due to an external dependency, write a comment explaining why it is implemented this way.


## Testing

* Keep the unit tests fast (usually this means milliseconds).
* Each test should clean up after itself.
* Each test must be isolated, do not rely on the result of another test.
* Do not rely on test order.
* Keep the tests simple, avoid the use of frameworks if possible (_do not use the Spring container in the test if it is not necessary_).
* Consistency, the tests should pass or fail in a consistent manner. If you have a test that is unstable, delete it/rewrite it.
* * Always run all tests before committing changes into the repository.

### Test naming

Name the unit test containing these parts **[[4]](#references)**:
1. **Unit of work**. What is the feature we are testing? (this is usually the method name)
2. **State under test**. What is the input to the test?
3. **Expected behavior**. What is the expected outcome`

If we take a simple example, we are going to write tests for this method:
```java
public class StringCalculator {
    public int add(String numbers) {
      ...
    }
}
```

The first scenario we want to test is when two numbers are sent in. In this case we should get the sum of the numbers in return.
```groovy
def "Add given two numbers returns sum"() {
  ...
}
```

Lets say that for negative numbers we expect the code to throw an `IllegalArgumentException`.
We can name the test like this:
```groovy
def "Add given negative number throws IllegalArgumentException"() {
  ...
}
```

### Asserts

A unit test should focus on testing one concept. A simple way to validate this is in the assert statements. If the unit test has multiple asserts on different values it is an indication that it is not testing one concept.

Therefore, keep the number of asserts to a minimum. This will help you test one concept at a time.

## References

1. [Robert C. Martin - Clean Code: A Handbook of Agile Software Craftsmanship](https://www.amazon.com/Clean-Code-Handbook-Software-Craftsmanship/dp/0132350882)
2. [Joshua Bloch - Effective Java (3rd Edition)](https://www.amazon.com/Effective-Java-3rd-Joshua-Bloch/dp/0134685997)
3. [Phil Karlton](https://martinfowler.com/bliki/TwoHardThings.html)
4. [Roy Osherove - Naming standards for unit tests](http://osherove.com/blog/2005/4/3/naming-standards-for-unit-tests.html)