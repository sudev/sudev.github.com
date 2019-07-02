---
categories:
- notes
comments: true
date: 2019-03-27T00:00:00Z
tags:
- design pattern
title: Design Patterns 
draft: true
---

# Design Patterns 

[TOC]



#### 

**Patterns**

*Solutions for a recurring problem.*

**Software Architecture** 

* It's about the components that make up a software, its interaction, its relationship with each other and how they will evolve over time. 
* Architect worries about the NFR, scalability...
* Examples - MVC, Container patterns.

**NFR** 

In [systems engineering](https://en.wikipedia.org/wiki/Systems_engineering) and [requirements engineering](https://en.wikipedia.org/wiki/Requirements_engineering), a **non-functional requirement** (NFR) is a [requirement](https://en.wikipedia.org/wiki/Requirement) that specifies criteria that can be used to judge the operation of a system, rather than specific behaviors. They are contrasted with [functional requirements](https://en.wikipedia.org/wiki/Functional_requirement) that define specific behavior or functions. 

The plan for implementing ***functional*** requirements is detailed in the [system *design*](https://en.wikipedia.org/wiki/Systems_design). 

The plan for implementing ***non-functional*** requirements is detailed in the [system *architecture*](https://en.wikipedia.org/wiki/Systems_architecture), because they are usually [architecturally significant requirements](https://en.wikipedia.org/wiki/Architecturally_significant_requirements).[[1\]](https://en.wikipedia.org/wiki/Non-functional_requirement#cite_note-ASR_Chen-1)

**Software Design** 

* Design work at lower level of abstraction based on the functionality, this dont really deal with the NFRs. 
* Solved using the **design patterns**. 

A good pattern design pattern will have the following traits.

* Flexibility 

* Reuse 
* Maintainability 



## GOF Design Patterns

- Creattional 
  - How objects are created 

* Structual 
  * How objects are composed.
* Behavioural 
  * Interactions 

*What's OO??*

* Encapsulation 
  * 
* Abstraction 
  * Hiding the details
* Inheritance 
  * SubClassing / Interfacing
  * Mutliple inheritance using interface

* Polymorphism 

# Creational Patterns 

In [software engineering](https://en.wikipedia.org/wiki/Software_engineering), **creational design patterns** are [design patterns](https://en.wikipedia.org/wiki/Design_pattern_(computer_science)) that deal with [object creation](https://en.wikipedia.org/wiki/Object_lifetime) mechanisms, trying to create objects in a manner suitable to the situation. The basic form of object creation could result in design problems or in added complexity to the design. 

*Creational design patterns solve this problem by somehow controlling this object creation.*

Creational design patterns are composed of two dominant ideas. 

* One is encapsulating knowledge about **which concrete classes** the system uses. 
* Another is hiding **how instances of these concrete classes are created and combined**.

Creational design patterns are further categorized into **Object-creational patterns** and **Class-creational patterns**, where Object-creational patterns deal with Object creation and Class-creational patterns deal with Class-instantiation. 

In greater details, 

* Object-creational patterns 
  * defer part of its object creation to another object.
* Class-creational patterns
  * defer its object creation to subclasses.

## Class creational patterns 

### Factory Method 

* Class expects the subclasses to decide howto create the objects. 

* https://en.wikipedia.org/wiki/Factory_method_pattern

  The Factory Method design pattern solves problems like: 

  - How can an object be created so that subclasses can redefine which class to instantiate?
  - How can a class defer instantiation to subclasses?

The Factory Method design pattern describes how to solve such problems:

- Define a separate operation (*factory method*) for creating an object.
- Create an object by calling a *factory method*.

* Issues 

  * Debugging is harder with most decisions being done at the runtime.

    

    

    The MazeGame uses Rooms but it puts the responsibility of creating Rooms to its subclasses which create the concrete classes. The regular game mode could use this template method:

```java
public abstract class Room {
   abstract void connect(Room room);
}

public class MagicRoom extends Room {
   public void connect(Room room) {}
}

public class OrdinaryRoom extends Room {
   public void connect(Room room) {}
}

public abstract class MazeGame {
    private final List<Room> rooms = new ArrayList<>();

    public MazeGame() {
        Room room1 = makeRoom();
        Room room2 = makeRoom();
        room1.connect(room2);
        rooms.add(room1);
        rooms.add(room2);
    }

    abstract protected Room makeRoom();
}
```

In the above snippet, the `MazeGame` constructor is a [template method](https://en.wikipedia.org/wiki/Template_method_pattern) that makes some common logic. It refers to the `makeRoom` factory method that encapsulates the creation of rooms such that other rooms can be used in a subclass. To implement the other game mode that has magic rooms, it suffices to override the `makeRoom` method:

```java
public class MagicMazeGame extends MazeGame {
    @Override
    protected Room makeRoom() {
        return new MagicRoom(); 
    }
}

public class OrdinaryMazeGame extends MazeGame {
    @Override
    protected Room makeRoom() {
        return new OrdinaryRoom(); 
    }
}

MazeGame ordinaryGame = new OrdinaryMazeGame();
MazeGame magicGame = new MagicMazeGame();
```



### Builder Pattern



* The intent of the Builder design pattern is to **separate the construction of a complex object** from **its representation.** 
* The Builder design pattern solves problems like:
  - How can a class (the same construction process) create different representations of a complex object?
  - How can a class that includes creating a complex object be simplified?

Java Example.

```java
/**
 * Represents the product created by the builder.
 */
class Car {
    private final int wheels;
    private final String color;

    // the constructor is not public, only the builder can create a Car
    Car(int wheels, String color) {
        this.wheels = wheels;
        this.color = color;
    }

    public int getWheels() {
        return wheels;
    }
    
    public String getColor() {
        return color;
    }

    @Override
    public String toString() {
        return "Car [wheels = " + wheels + ", color = " + color + "]";
    }
}

/**
 * A builder of Car.
 */
class CarBuilder {
    private int wheels = 0;
    private String color = "";

    public CarBuilder setColor(String color) {
        this.color = color;
        return this;
    }

    public CarBuilder setWheels(int wheels) {
        this.wheels = wheels;
        return this;
    }
    
    public Car build() {
        return new Car(wheels, color);
    }
}

public class BuilderDemo {
    public static void main(String[] arguments) {
        Car car = new CarBuilder()
            .setWheels(4)
            .setColor("Red")
            .build();
        System.out.println(car);
    }
}
	
```

```scala
/**
 * Represents the product created by the builder.
 */
case class Car(wheels:Int, color:String)

/**
 * The builder abstraction.
 */
trait CarBuilder {
    def setWheels(wheels:Int) : CarBuilder
    def setColor(color:String) : CarBuilder
    def build() : Car
}

class CarBuilderImpl extends CarBuilder {
    private var wheels:Int = 0
    private var color:String = ""

    override def setWheels(wheels:Int) = {
        this.wheels = wheels
        this
    }

    override def setColor(color:String) = {
        this.color = color
        this
    }

    override def build = Car(wheels,color)
}

class CarBuildDirector(private val builder : CarBuilder) {
    def construct = builder.setWheels(4).setColor("Red").build

    def main(args: Array[String]): Unit = {
        val builder = new CarBuilderImpl
        val carBuildDirector = new CarBuildDirector(builder)
        println(carBuildDirector.construct) 
    }
}
```

### Singleton Pattern

Restricts the [instantiation](https://en.wikipedia.org/wiki/Instantiation_(computer_science)) of a [class](https://en.wikipedia.org/wiki/Class_(computer_programming)) to one "single" instance. This is useful when exactly one object is needed to **coordinate actions across the system.** 

The singleton design pattern solves problems like:

- How can it be ensured that a class has only one instance?
- How can the sole instance of a class be accessed easily?
- How can a class control its instantiation?
- How can the number of instances of a class be restricted?

Making a Logger a singleton class will make sure that there wont be any overwrites to log files with multiple instances trying to write the logs. Singletons can also be used to keep counters, generate unique ids.

The instance is usually stored as a private [static variable](https://en.wikipedia.org/wiki/Static_variable); the instance is created when the variable is initialized, at some point before the static method is first called. The following is a sample implementation written in Java.

```java
public final class Singleton {

    private static final Singleton INSTANCE = new Singleton();

    private Singleton() {}

    public static Singleton getInstance() {
        return INSTANCE;
    }
}
```

**Lazy initialization**

A singleton implementation may use [lazy initialization](https://en.wikipedia.org/wiki/Lazy_initialization), where the instance is created when the static method is first invoked. If the static method might be called from multiple threads simultaneously, measures may need to be taken to prevent [race conditions](https://en.wikipedia.org/wiki/Race_condition) that could result in the creation of multiple instances of the class. The following is a [thread-safe](https://en.wikipedia.org/wiki/Thread_safety) sample implementation, using lazy initialization with [double-checked locking](https://en.wikipedia.org/wiki/Double-checked_locking), written in Java.

```java
public final class Singleton {

    private static volatile Singleton instance = null;

    private Singleton() {}

    public static Singleton getInstance() {
        if (instance == null) {
            synchronized(Singleton.class) {
                if (instance == null) {
                    instance = new Singleton();
                }
            }
        }

        return instance;
    }
}
```

### Prototype 

Used when the type of [objects](https://en.wikipedia.org/wiki/Object_(computer_science)) to create is determined by a [prototypical](https://en.wikipedia.org/wiki/Prototype) [instance](https://en.wikipedia.org/wiki/Instance_(computer_science)), which is cloned to produce new objects. This pattern is used to:

- avoid [subclasses](https://en.wikipedia.org/wiki/Subclass_(computer_science)) of an object creator in the client application, like the [factory method pattern](https://en.wikipedia.org/wiki/Factory_method_pattern) does.
- avoid the inherent cost of creating a new object in the standard way (e.g., using the '[new](https://en.wikipedia.org/wiki/New_(C%2B%2B))' keyword) when it is prohibitively expensive for a given application.

The Prototype design pattern solves problems like: 

- How can objects be created so that which objects to create can be specified at run-time?
- How can dynamically loaded classes be instantiated?

This pattern creates the kind of object using its prototype. In other words, while creating the object of Prototype object, the class actually creates a clone of it and returns it as prototype. You can see here, we have used Clone method to clone the prototype when required.

```java
// Prototype pattern
public abstract class Prototype implements Cloneable {
    public Prototype clone() throws CloneNotSupportedException{
        return (Prototype) super.clone();
    }
}
	
public class ConcretePrototype1 extends Prototype {
    @Override
    public Prototype clone() throws CloneNotSupportedException {
        return (ConcretePrototype1)super.clone();
    }
}

public class ConcretePrototype2 extends Prototype {
    @Override
    public Prototype clone() throws CloneNotSupportedException {
        return (ConcretePrototype2)super.clone();
    }
}
```

### Object Pool 

* *Uses a set of initialized objects – rather than allocating and destroying them on demand*. 
* A client of the pool will request an object from the pool and perform operations on the returned object. When the client has finished, it returns the object to the pool rather than [destroying it](https://en.wikipedia.org/wiki/Object_destruction); this can be done manually or automatically.

* Object pools are primarily used for performance: in some circumstances, object pools significantly improve performance.
* Object pools complicate [object lifetime](https://en.wikipedia.org/wiki/Object_lifetime), as objects obtained from and returned to a pool are not actually created or destroyed at this time, and thus require care in implementation.



```java
public class PooledObjectPool {
	private static long expTime = 6000;//6 seconds
	public static HashMap<PooledObject, Long> available = new HashMap<PooledObject, Long>();
	public static HashMap<PooledObject, Long> inUse = new HashMap<PooledObject, Long>();
	
	public synchronized static PooledObject getObject() {
		long now = System.currentTimeMillis();
		if (!available.isEmpty()) {
			for (Map.Entry<PooledObject, Long> entry : available.entrySet()) {
				if (now - entry.getValue() > expTime) { //object has expired
					popElement(available);
				} else {
					PooledObject po = popElement(available, entry.getKey());
					push(inUse, po, now); 
					return po;
				}
			}
		}

		// either no PooledObject is available or each has expired, so return a new one
		return createPooledObject(now);
	}	
	
	private synchronized static PooledObject createPooledObject(long now) {
		PooledObject po = new PooledObject();
		push(inUse, po, now);
		return po;
        }

	private synchronized static void push(HashMap<PooledObject, Long> map,
			PooledObject po, long now) {
		map.put(po, now);
	}

	public static void releaseObject(PooledObject po) {
		cleanUp(po);
		available.put(po, System.currentTimeMillis());
		inUse.remove(po);
	}
	
	private static PooledObject popElement(HashMap<PooledObject, Long> map) {
		 Map.Entry<PooledObject, Long> entry = map.entrySet().iterator().next();
		 PooledObject key= entry.getKey();
		 //Long value=entry.getValue();
		 map.remove(entry.getKey());
		 return key;
	}
	
	private static PooledObject popElement(HashMap<PooledObject, Long> map, PooledObject key) {
		map.remove(key);
		return key;
	}
	
	public static void cleanUp(PooledObject po) {
		po.setTemp1(null);
		po.setTemp2(null);
		po.setTemp3(null);
	}
}
```

# Structural

### Adapter 

The adapter design pattern solves problems like:

- How can a class be reused that does not have an interface that a client requires?
- How can classes that have incompatible interfaces work together?
- How can an alternative interface be provided for a class?

Often an (already existing) class can't be reused only because its interface doesn't conform to the interface clients require.

The adapter design pattern describes how to solve such problems:

- Define a separate `adapter` class that converts the (incompatible) interface of a class (`adaptee`) into another interface (`target`) clients require.
- Work through an `adapter` to work with (reuse) classes that do not have the required interface.

```java
interface LightningPhone {
  void recharge();
  void useLightning();
}

interface MicroUsbPhone {
  void recharge();
  void useMicroUsb();
}

class Iphone implements LightningPhone {
  private boolean connector;

  @Override
  public void useLightning() {
    connector = true;
    System.out.println("Lightning connected");
  }

  @Override
  public void recharge() {
    if (connector) {
      System.out.println("Recharge started");
      System.out.println("Recharge finished");
    } else {
      System.out.println("Connect Lightning first");
    }
  }
}

class Android implements MicroUsbPhone {
  private boolean connector;

  @Override
  public void useMicroUsb() {
    connector = true;
    System.out.println("MicroUsb connected");
  }

  @Override
  public void recharge() {
    if (connector) {
      System.out.println("Recharge started");
      System.out.println("Recharge finished");
    } else {
      System.out.println("Connect MicroUsb first");
    }
  }
}

class LightningToMicroUsbAdapter implements MicroUsbPhone {
  private final LightningPhone lightningPhone;

  public LightningToMicroUsbAdapter(LightningPhone lightningPhone) {
    this.lightningPhone = lightningPhone;
  }

  @Override
  public void useMicroUsb() {
    System.out.println("MicroUsb connected");
    lightningPhone.useLightning();
  }

  @Override
  public void recharge() {
    lightningPhone.recharge();
  }
}

public class AdapterDemo {
  static void rechargeMicroUsbPhone(MicroUsbPhone phone) {
    phone.useMicroUsb();
    phone.recharge();
  }

  static void rechargeLightningPhone(LightningPhone phone) {
    phone.useLightning();
    phone.recharge();
  }

  public static void main(String[] args) {
    var android = new Android();
    var iPhone = new Iphone();

    System.out.println("Recharging android with MicroUsb");
    rechargeMicroUsbPhone(android);

    System.out.println("Recharging iPhone with Lightning");
    rechargeLightningPhone(iPhone);

    System.out.println("Recharging iPhone with MicroUsb");
    rechargeMicroUsbPhone(new LightningToMicroUsbAdapter(iPhone));
  }
}
```

### Bridge Pattern 

```scala
trait DrawingAPI {
  def drawCircle(x: Double, y: Double, radius: Double)
}

class DrawingAPI1 extends DrawingAPI {
  def drawCircle(x: Double, y: Double, radius: Double) = println(s"API #1 $x $y $radius")
}

class DrawingAPI2 extends DrawingAPI {
  def drawCircle(x: Double, y: Double, radius: Double) = println(s"API #2 $x $y $radius")
}

abstract class Shape(drawingAPI: DrawingAPI) {
  def draw()
  def resizePercentage(pct: Double)
}

class CircleShape(x: Double, y: Double, var radius: Double, drawingAPI: DrawingAPI)
    extends Shape(drawingAPI: DrawingAPI) {

  def draw() = drawingAPI.drawCircle(x, y, radius)

  def resizePercentage(pct: Double) { radius *= pct }
}

object BridgePattern {
  def main(args: Array[String]) {
    Seq (
	new CircleShape(1, 3, 5, new DrawingAPI1),
	new CircleShape(4, 5, 6, new DrawingAPI2)
    ) foreach { x =>
        x.resizePercentage(3)
        x.draw()			
      }	
  }
}
```

### Composite Pattern 

*Describes a group of objects that is treated the same way as a single instance of the same type of object.* 

The intent of a composite is to "compose" objects into tree structures to represent *part-whole hierarchies*. Implementing the composite pattern lets clients treat individual objects and compositions uniformly.

What problems can the Composite design pattern solve? 

- A part-whole hierarchy should be represented so that clients can treat part and whole objects uniformly.
- A part-whole hierarchy should be represented as tree structure.

When defining (1) `Part` objects and (2) `Whole` objects that act as containers for `Part` objects, clients must treat them separately, which complicates client code.

What solution does the Composite design pattern describe?

- Define a unified `Component` interface for both part (`Leaf`) objects and whole (`Composite`) objects.
- Individual `Leaf` objects implement the `Component` interface directly, and `Composite` objects forward requests to their child components.

```java
import java.util.ArrayList;

/** "Component" */
interface Graphic {
    //Prints the graphic.
    public void print();
}

/** "Composite" */
class CompositeGraphic implements Graphic {
    //Collection of child graphics.
    private final ArrayList<Graphic> childGraphics = new ArrayList<>();

    //Adds the graphic to the composition.
    public void add(Graphic graphic) {
        childGraphics.add(graphic);
    }
    
    //Prints the graphic.
    @Override
    public void print() {
        for (Graphic graphic : childGraphics) {
            graphic.print();  //Delegation
        }
    }
}

/** "Leaf" */
class Ellipse implements Graphic {
    //Prints the graphic.
    @Override
    public void print() {
        System.out.println("Ellipse");
    }
}

/** Client */
public class CompositeDemo {
    public static void main(String[] args) {
        //Initialize four ellipses
        Ellipse ellipse1 = new Ellipse();
        Ellipse ellipse2 = new Ellipse();
        Ellipse ellipse3 = new Ellipse();
        Ellipse ellipse4 = new Ellipse();

        //Creates two composites containing the ellipses
        CompositeGraphic graphic2 = new CompositeGraphic();
        graphic2.add(ellipse1);
        graphic2.add(ellipse2);
        graphic2.add(ellipse3);
        
        CompositeGraphic graphic3 = new CompositeGraphic();
        graphic3.add(ellipse4);
        
        //Create another graphics that contains two graphics
        CompositeGraphic graphic1 = new CompositeGraphic();
        graphic1.add(graphic2);
        graphic1.add(graphic3);

        //Prints the complete graphic (Four times the string "Ellipse").
        graphic1.print();
    }
}
```

### Decorator Pattern 



*Allows behavior to be added to an individual object, dynamically, without affecting the behavior of other objects from the same class.*

The following Java example illustrates the use of decorators using the window/scrolling scenario.

```java
// The Window interface class
public interface Window {
    void draw(); // Draws the Window
    String getDescription(); // Returns a description of the Window
}

// Implementation of a simple Window without any scrollbars
class SimpleWindow implements Window {
    @Override
    public void draw() {
        // Draw window
    }
    @Override
    public String getDescription() {
        return "simple window";
    }
}
```

The following classes contain the decorators for all `Window` classes, including the decorator classes themselves.

```java
// abstract decorator class - note that it implements Window
abstract class WindowDecorator implements Window {
    private final Window windowToBeDecorated; // the Window being decorated

    public WindowDecorator (Window windowToBeDecorated) {
        this.windowToBeDecorated = windowToBeDecorated;
    }
    @Override
    public void draw() {
        windowToBeDecorated.draw(); //Delegation
    }
    @Override
    public String getDescription() {
        return windowToBeDecorated.getDescription(); //Delegation
    }
}

// The first concrete decorator which adds vertical scrollbar functionality
class VerticalScrollBarDecorator extends WindowDecorator {
    public VerticalScrollBarDecorator (Window windowToBeDecorated) {
        super(windowToBeDecorated);
    }

    @Override
    public void draw() {
        super.draw();
        drawVerticalScrollBar();
    }

    private void drawVerticalScrollBar() {
        // Draw the vertical scrollbar
    }

    @Override
    public String getDescription() {
        return super.getDescription() + ", including vertical scrollbars";
    }
}

// The second concrete decorator which adds horizontal scrollbar functionality
class HorizontalScrollBarDecorator extends WindowDecorator {
    public HorizontalScrollBarDecorator (Window windowToBeDecorated) {
        super(windowToBeDecorated);
    }

    @Override
    public void draw() {
        super.draw();
        drawHorizontalScrollBar();
    }

    private void drawHorizontalScrollBar() {
        // Draw the horizontal scrollbar
    }

    @Override
    public String getDescription() {
        return super.getDescription() + ", including horizontal scrollbars";
    }
}
```

Here's a test program that creates a `Window` instance which is fully decorated (i.e., with vertical and horizontal scrollbars), and prints its description:

```java
public class DecoratedWindowTest {
    public static void main(String[] args) {
        // Create a decorated Window with horizontal and vertical scrollbars
        Window decoratedWindow = new HorizontalScrollBarDecorator (
                new VerticalScrollBarDecorator (new SimpleWindow()));

        // Print the Window's description
        System.out.println(decoratedWindow.getDescription());
    }
}
```



### Flyweight Pattern 



```scala
/* the `private` constructor ensures that only interned
 * values of type `CoffeeFlavour` can be obtained. */
class CoffeeFlavour private (val name: String){
    override def toString = s"CoffeeFlavour($name)"
}

object CoffeeFlavour {
  import scala.collection.mutable
  import scala.ref.WeakReference
  private val cache = mutable.Map.empty[String, ref.WeakReference[CoffeeFlavour]]

  def apply(name: String): CoffeeFlavour = synchronized {    
    cache.get(name) match {
      case Some(WeakReference(flavour)) => flavour
      case _ => 
        val newFlavour = new CoffeeFlavour(name)
        cache.put(name, WeakReference(newFlavour))
        newFlavour
    }
  }

  def totalCoffeeFlavoursMade = cache.size
}


case class Order(tableNumber: Int, flavour: CoffeeFlavour){

  def serve: Unit =
    println(s"Serving $flavour to table $tableNumber")
}

object CoffeeShop {
  var orders = List.empty[Order]

  def takeOrder(flavourName: String, table: Int) {
    val flavour = CoffeeFlavour(flavourName)
    val order = Order(table, flavour)
    orders = order :: orders
  }

  def service: Unit = orders.foreach(_.serve)

  def report =
    s"total CoffeeFlavour objects made: ${CoffeeFlavour.totalCoffeeFlavoursMade}"
}


CoffeeShop.takeOrder("Cappuccino", 2)
CoffeeShop.takeOrder("Frappe", 1)
CoffeeShop.takeOrder("Espresso", 1)
CoffeeShop.takeOrder("Frappe", 897)
CoffeeShop.takeOrder("Cappuccino", 97)
CoffeeShop.takeOrder("Frappe", 3)
CoffeeShop.takeOrder("Espresso", 3)
CoffeeShop.takeOrder("Cappuccino", 3)
CoffeeShop.takeOrder("Espresso", 96)
CoffeeShop.takeOrder("Frappe", 552)
CoffeeShop.takeOrder("Cappuccino", 121)
CoffeeShop.takeOrder("Espresso", 121)

CoffeeShop.service
println(CoffeeShop.report)
```

### Proxy Pattern 



```java
interface Image {
    public void displayImage();
}

// On System A
class RealImage implements Image {
    private final String filename;

    /**
     * Constructor
     * @param filename
     */
    public RealImage(String filename) {
        this.filename = filename;
        loadImageFromDisk();
    }

    /**
     * Loads the image from the disk
     */
    private void loadImageFromDisk() {
        System.out.println("Loading   " + filename);
    }

    /**
     * Displays the image
     */
    public void displayImage() {
        System.out.println("Displaying " + filename);
    }
}

// On System B
class ProxyImage implements Image {
    private final String filename;
    private RealImage image;
    
    /**
     * Constructor
     * @param filename
     */
    public ProxyImage(String filename) {
        this.filename = filename;
    }

    /**
     * Displays the image
     */
    public void displayImage() {
        if (image == null) {
           image = new RealImage(filename);
        }
        image.displayImage();
    }
}

class ProxyExample {
   /**
    * Test method
    */
   public static void main(final String[] arguments) {
        Image image1 = new ProxyImage("HiRes_10MB_Photo1");
        Image image2 = new ProxyImage("HiRes_10MB_Photo2");

        image1.displayImage(); // loading necessary
        image1.displayImage(); // loading unnecessary
        image2.displayImage(); // loading necessary
        image2.displayImage(); // loading unnecessary
        image1.displayImage(); // loading unnecessary
    }
}
```



### Command Pattern 

TODO 

https://en.wikipedia.org/wiki/Command_pattern

### Observer Pattern 

*An object, called the **subject**, maintains a list of its dependents, called **observers**, and notifies them automatically of any state changes, usually by calling one of their methods.*

* It is mainly used to implement distributed [event handling](https://en.wikipedia.org/wiki/Event_handling) systems, in "event driven" software.

```java
import java.util.ArrayList;
import java.util.Scanner;

class EventSource {
    public interface Observer {
        void update(String event);
    }
  
    private final ArrayList<Observer> observers = new ArrayList<>();
  
    private void notifyObservers(String event) {
        observers.forEach(observer -> observer.update(event));
    }
  
    public void addObserver(Observer observer) {
        observers.add(observer);
    }
  
    public void scanSystemIn() {
        var scanner = new Scanner(System.in);
        while (scanner.hasNextLine()) {
            var line = scanner.nextLine();
            notifyObservers(line);
        }
    }
}
```

```java
public class ObserverDemo {
    public static void main(String[] args) {
        System.out.println("Enter Text: ");
        var eventSource = new EventSource();
        
        eventSource.addObserver(event -> {
            System.out.println("Received response: " + event);
        });

        eventSource.scanSystemIn();
    }
}
```

##### What problems can the Observer design pattern solve?

The Observer pattern addresses the following problems:

- A one-to-many dependency between objects should be defined without making the objects tightly coupled.
- It should be ensured that when one object changes state an open-ended number of dependent objects are updated automatically.
- It should be possible that one object can notify an open-ended number of other objects.

Defining a one-to-many dependency between objects by defining one *object (subject)* that updates the state of dependent objects directly is inflexible because it couples the subject to particular dependent objects. Tightly coupled objects are hard to implement, change, test, and reuse because they refer to and know about (how to update) many different objects with different interfaces.

##### What solution does the Observer design pattern describe?

- Define `Subject` and `Observer` objects.
- so that when a subject changes state, all registered observers are notified and updated automatically.

The sole responsibility of a subject is to maintain a list of observers and to notify them of state changes by calling their `update()` operation. 

The responsibility of observers is to register (and unregister) themselves on a subject (to get notified of state changes) and to update their state (synchronize their state with subject's state) when they are notified. 
This makes subject and observers loosely coupled. Subject and observers have no explicit knowledge of each other. Observers can be added and removed independently at run-time.

This notification-registration interaction is also known as [publish-subscribe](https://en.wikipedia.org/wiki/Publish-subscribe).

## Behavioural Patterns

### Strategy Pattern

*Instead of implementing a single algorithm directly, code receives run-time instructions as to which in a family of algorithms to use.*

````java
import java.util.ArrayList;

@FunctionalInterface
interface BillingStrategy {
    // use a price in cents to avoid floating point round-off error
    int getActPrice(int rawPrice);
  
    //Normal billing strategy (unchanged price)
    static BillingStrategy normalStrategy() {
        return rawPrice -> rawPrice;
    }
  
    //Strategy for Happy hour (50% discount)
    static BillingStrategy happyHourStrategy() {
        return rawPrice -> rawPrice / 2;
    }
}

class Customer {
    private final ArrayList<Integer> drinks = new ArrayList<>();
    private BillingStrategy strategy;

    public Customer(BillingStrategy strategy) {
        this.strategy = strategy;
    }

    public void add(int price, int quantity) {
        this.drinks.add(this.strategy.getActPrice(price*quantity));
    }

    // Payment of bill
    public void printBill() {
        int sum = this.drinks.stream().mapToInt(v -> v).sum();
        System.out.println("Total due: " + sum / 100.0);
        this.drinks.clear();
    }

    // Set Strategy
    public void setStrategy(BillingStrategy strategy) {
        this.strategy = strategy;
    }
}

public class StrategyPattern {
    public static void main(String[] arguments) {
        // Prepare strategies
        BillingStrategy normalStrategy    = BillingStrategy.normalStrategy();
        BillingStrategy happyHourStrategy = BillingStrategy.happyHourStrategy();

        Customer firstCustomer = new Customer(normalStrategy);

        // Normal billing
        firstCustomer.add(100, 1);

        // Start Happy Hour
        firstCustomer.setStrategy(happyHourStrategy);
        firstCustomer.add(100, 2);

        // New Customer
        Customer secondCustomer = new Customer(happyHourStrategy);
        secondCustomer.add(80, 1);
        // The Customer pays
        firstCustomer.printBill();

        // End Happy Hour
        secondCustomer.setStrategy(normalStrategy);
        secondCustomer.add(130, 2);
        secondCustomer.add(250, 1);
        secondCustomer.printBill();
    }
}
````

### Template method

This pattern has two main parts, and typically uses object-oriented programming:

- The "template method", generally implemented as a [base class](https://en.wikipedia.org/wiki/Base_class) (possibly an [abstract class](https://en.wikipedia.org/wiki/Abstract_class)), which contains **shared code and parts of the overall algorithm which are invariant**. The template ensures that the overarching algorithm is always followed. In this class, "variant" portions are given a default implementation, or none at all.
- Concrete implementations of the abstract class, **which fill in the empty or "variant" parts** of the "template" with specific algorithms that vary from implementation to implementation.