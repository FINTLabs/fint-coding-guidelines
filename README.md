# FINT coding guidelines

These guidelines were created to ensure consistency within FINT source code.


## 1. General

### 1.1 Code style

FINT uses the default (Java code style in IntelliJ IDEA)[https://www.jetbrains.com/help/idea/code-style-java.html].  

1. Always format the code before committing the code into the repository.  
2.  If the code requires reformatting while you are working on another task, add the format changes to its own commit. This makes it easier to review the code. 


### 1.1 Small methods/classes

_The first rule of functions is that they should be small._
_The second rule of functions is that they should be smaller than that._
- Robert C. Martin (Clean code)

Functions should do one thing. They should do it well. They should do it only.
- Robert C. Martin (Clean code)

A class should have a single responsibility. It should have a single reason to change.
Methods should do "one thing". Try to keep all logic in a method on the same level of abstraction.

It is difficult to set a number of lines is too much for a class/method, but we use these guidelines:
1. Is the method tens of lines long, perhaps it is working on multiple levels of abstraction? 
2. Is the class hundreds of lines long, perhaps the class has more than one responsibilty?
3. Is the class name very generic? The class is most likely dealing with multiple responsibilities.
4. Do you have a hard time finding a good name for a class? It could be  separate concepts within the same class and that it should be split into two classes.


### 1.2 Naming

There are only two hard things in Computer Science: cache invalidation and naming things.
- Phil Karlton (https://martinfowler.com/bliki/TwoHardThings.html)

The name of a variable, function, or class, should answer all the big questions. It should tell you why it exists, what it does, and how it is used. If a name requires a comment, then the name does not reveal its intent.
- Robert C. Martin (Clean code)

The length of a name should correspond to the size of its scope.
- Robert C. Martin (Clean code)

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



### 1.4 Comments

## 2. Testing

### 2.1 Test naming

### 2.2 Asserts

### 2.3 Mocking