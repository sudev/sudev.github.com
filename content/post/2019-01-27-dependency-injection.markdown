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

# Dependency Injection

- Emailer depends on a SpellChecker, a TextEditor, and an AddressBook.
- This relationship is called a dependency. In other words, Emailer is a *client* of its dependencies.
- *Composition* also applies *transitively*; an object may depend on other objects that themselves have dependencies, and so on.


Service

    An object that performs a well-defined function when called upon.
Client
    
    Any consumer of a service; an object that calls upon a service to perform a well-understood function.
    

Dependency 
    
    A specific service that is required by another object to fulfill its function.
Dependent
    
    A client object that needs a dependency (or dependencies) in order to perform its function.


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

