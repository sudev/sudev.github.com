---
categories:
- notes
comments: true
date: 2019-01-27T00:00:00Z
tags:
- OOPs
- Java
- Dependency Injection
title: Dependency Injection
draft: true
---



- Emailer depends on a SpellChecker, a TextEditor, and an AddressBook.
- This relationship is called a dependency. In other words, Emailer is a *client* of its dependencies.
- *Composition* also applies *transitively*; an object may depend on other objects that themselves have dependencies, and so on.


- Service    
*An object that performs a well-defined function when called upon.*
- Client   
*Any consumer of a service; an object that calls upon a service to perform a well-understood function.*
- Dependency   
*A specific service that is required by another object to fulfill its function.*     
- Dependent    
*A client object that needs a dependency (or dependencies) in order to perform its function.*


## Pre-DI Solutions

Assume you have a spell-checker which is a dependency for the emailer client. 

Here is a sample implementation of emailer client.

```java 
public class Emailer {
    private SpellChecker spellChecker;
    public Emailer() {
        this.spellChecker = new SpellChecker();
    }

    public void send(String text) { .. }
}
```

Now, let's say you want to write a unit test for the send() method to ensure that it is checking spelling before sending any message. How would you do it? You might create a mock SpellChecker and give that to the Emailer. Something like:

```java
public class MockSpellChecker extends SpellChecker { 
    public boolean didSpellCheckSpelling = false;

    public boolean checkSpelling(String text) { 
        didSpellCheckSpelling = true;
        return true;
    }

    public boolean verfyDidCheckSpelling() { 
        return didSpellCheckSpelling
    }

}
```
Of course, we can't use this mock because we are unable to substitute the internal spellchecker that an Emailer has. This effectively makes your class **untestable**, which is a showstopper for this approach.


Now let's say that you want to have a English specific spellchecker. This implementation is also so tied to the EnglishSpellChecker for the Emailer object essentially making it impossible for you to instantiate a Emailer with HindiSpellChecker. 

```java 
public Emailer() {
        this.spellChecker = new EnglishSpellChecker();
    }
    ...
}
```

### Construction by hand 

```java 
public class Emailer {
    private SpellChecker spellChecker;
    public void setSpellChecker(SpellChecker spellChecker) {
        this.spellChecker = spellChecker;
    }
    ...
}
```
We replace the constructor to take SpellChecker as a parameter, this way we can create Emailer with different SpellChecker, test the Emailer class and so on. 

```java 
public void ensureEmailerCheckerSpelling() { 
    MockSpellingCehcker mock = new MockSpellingChecker();
    Emailer emailer = new Emailer();

    // Pass the mock object for testing.
    emailer.setSpellChecker(mock);
    emailer.send("Hello There?");

    // Verify the dependency used.
    assert mock.verifyCheckSpelling();
}
```

Similarly, it is easy to construct Emailers with various behaviors. Here's one for French spelling:


```java 
Emailer service = new Emailer();
// Constructor takes a SpellChecker.
service.setSpellChecker(new FrenchSpellChecker());
```

Since you end up connecting the pipes yourself at the time of construction, this technique is referred to as *construction by hand*. 

Taking the SpellChecker within the constructor, no setter method. Even more consice than the setter method.

```java 
public class Emailer {
    private SpellChecker spellChecker;
    public Emailer(SpellChecker spellChecker) {
        this.spellChecker = spellChecker;
    }
    ...
}

// Initialising an Emailer object in this case.
Emailer service = new Emailer(new JapaneseSpellChecker());
```

Problems with the constrution by hand approach. 

- While construction by hand definitely helps with testing, it has some problems, the most obvious one being the burden of **knowing how to construct object graphs being placed on the client of a service**.
- **Repeatition of code** for wiring objects in all places.
- If you alter the dependency graph or any of its parts, you may be forced to go through and **alter all of its clients as well**.
- Since everything is wired manually this **violates the principle of encapsulation** and becomes problematic when dealing with code that is used by many clients.

### The factory method

* Another time-honored method of constructing object graphs is the Factory design pattern (also known as the Abstract Factory[1] pattern). 
* The idea behind the Factory pattern is to offload the burden of creating dependencies to a third-party object called a factory.

##### An email service whose spellchecker is set via constructor

```java
public class Emailer {
    private SpellChecker spellChecker;
    public Emailer(SpellChecker spellChecker) {
        this.spellChecker = spellChecker;
    }
    ...
}
```

Instead of constructing the object graph by hand, we do it inside another class called a factory.

A "French" email service Factory pattern

```java
public class EmailerFactory {
    public Emailer newFrenchEmailer() {
        return new Emailer(new FrenchSpellChecker());
    }
}
```

Notice that the Factory pattern is very explicit about what kind of Emailer it is going to produce; newFrenchEmailer() creates one with a French spellchecker. Any code that uses French email services is now fairly straightforward:

```java
Emailer service = new EmailerFactory().newFrenchEmailer();
```
* The most important thing to notice here is that the client code has no reference to spellchecking, address books, or any of the other internals of Emailer. 
* By adding a level of abstraction (the Factory pattern), we have *separated the code using the Emailer from the code that creates the Emailer*. This leaves client code clean and concise.
* The beauty of this approach is that client code only needs to know which Factory to use to obtain a dependency.