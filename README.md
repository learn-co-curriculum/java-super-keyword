# Using the keyword super: Inheritance and Interfaces

## Learning Goals

- Compare the different uses of the `super` keyword

## Introduction

This lesson will compare the various ways the keyword `super`
is used to access a method of a parent class or interface.

## Calling the Superclass Constructor

![person hierarchy with constructors](https://curriculum-content.s3.amazonaws.com/6677/pillars/person_hierarchy_constructors2.png)

As we've seen in the lesson on constructors and inheritance, we use the 
`super` keyword to call the superclass constructor.
This must be the first statement in every constructor.  

```java
public class Person {

    private String name;
    private int age;

    public Person() {
        super();   //calls Object() constructor
    }

    public Person(String name, int age) {
        super();   //calls Object() constructor
        this.name = name;
        this.age = age;
    }
    
    // other methods
}
```

```java
public class Teacher extends Person {

    public Teacher() {
        super();   //calls Person() constructor
    }

    public Teacher(String name, int age) {
        super(name, age);  //calls Person(String name, int age) constructor
    }
    
    // other methods

}
```

```java
public class Student extends Person {
    private String favoriteSubject;

    public Student() {
        super();   //calls Person() constructor
    }

    public Student(String name, int age, String favoriteSubject) {
        super(name, age);   //calls Person(String name, int age) constructor
        this.favoriteSubject = favoriteSubject;
    }

    // other methods
}
```

Remember that the Java compiler automatically inserts a call to the no-argument constructor
`super()`, unless the programmer explicitly calls a superclass constructor.

## Calling An Overriden Superclass Method

A class can override an inherited method.
Sometimes we want the overriding method to reuse
the implementation defined in the superclass.  
We can use the keyword `super` to invoke the method implemented in the superclass.

![product class hierarchy](https://curriculum-content.s3.amazonaws.com/6677/pillars/product_class_hierarchy.png)

As an example, consider the `Product` class hierarchy:

```java
public class Product {
  public String getName() { return "Widget";}
  public double getPrice() { return 80.0;}
}
```

```java
public class NonreturnableProduct extends Product {
  @Override
  public String getName() {return super.getName() + "-nonreturnable";}  //Widget-nonreturnable
}
```

- The `NonreturnableProduct` class overrides `getName()`.
  - `super.getName()` executes the method body defined in the `Product` class.
- The `NonreturnableProduct` class inherits `getPrice()` from the `Product` class. 


```java
public class DamagedProduct extends NonreturnableProduct {
  @Override
  public String getName() {return super.getName()+"-damaged";} //Widget-nonreturnable-damaged

  @Override
  public double getPrice() {return super.getPrice() * 0.5;}    //80 * 0.5 = 40
}
```

- The `DamagedProduct` class overrides `getName()`.
  - `super.getName()` executes the method body defined in `NonreturnableProduct`.
- The `DamagedProduct` class overrides `getPrice()`.
  - `super.getPrice()` executes the method body defined in `Product` (since `NonreturnableProduct` inherits it from `Product`).

The `main()` method creates instances of each class and calls the methods:

```java
public class Main {

  public static void printInfo(Product p) {
    System.out.println(p.getName() + " costs $" + p.getPrice());
  }
  public static void main(String[] args) {
    printInfo(new Product());
    printInfo(new NonreturnableProduct());
    printInfo(new DamagedProduct());
  }
}
```

The program prints:

```text
Widget costs $80.0
Widget-nonreturnable costs $80.0
Widget-nonreturnable-damaged costs $40.0
```

### Why can't we write `super.super.m()` to call an inherited method?

A common error is to attempt to use the reference `super.super` to access a member of a grandparent
class (i.e. the parent of a parent class). We would get a compiler error if we tried to
write `super.super.getPrice()`  or `super.super.getName()` within a method of the `DamagedProduct` class.

As we saw in the `getPrice()` method implemented in the `DamagedProduct` class,
we can simply write `super.getPrice()` to execute the method implemented in `Product` because
the `NonreturnableProduct` class inherits and does not override it.

Once a child class overrides a method from a parent class, descendants of the child class
can't access the original parent's implementation.  That is why methods in `DamagedProduct`
can't use the expression `super.super.getName()` to execute the method body defined in `Product`.
The `DamagedProduct` class is restricted to executing methods inherited from its immediate
parent `NonreturnableProduct`, which include the `getPrice()` inherited from `Product` and the `getName()`
overriden in `NonreturnableProduct`.

## Calling An Overriden Interface Method

The lesson on default and static interface methods showed yet another use of the `super` keyword, specifically
when a class implements multiple interfaces having a default method with the same name.

![updated-uml-with-static-method](https://curriculum-content.s3.amazonaws.com/java-mod-3/static-default-methods-interfaces/uml-vehicle-alarm-car-static-method.PNG)

`Car` inherits default methods `turnAlarmOn` and `turnAlarmOff` from both `Vehicle` and `Alarm` and
won't compile unless the methods are overriden within `Car`.  One option is to call a
specific parent interface implementation of `turnAlarmOn` and
`turnAlarmOff` using either `Vehicle.super` or `Alarm.super`.
For example:

```java
public class Car implements Vehicle, Alarm {

    // fields and methods ...

    @Override
    public String turnAlarmOn() {
        return Vehicle.super.turnAlarmOn();
    }

    @Override
    public String turnAlarmOff() {
        return Vehicle.super.turnAlarmOff();
    }
}
```


## Extending a single class, implementing multiple interfaces 

A Java class can only extend one parent class, multiple inheritance is not allowed.
Within the subclass `DiscountProduct`, we use the syntax `super.getPrice()` to call
the overriden method defined in the superclass `Product`.  We don't need to include the superclass
name in the method call since there can only be one superclass.

Interfaces are not classes, however, and a class can implement multiple interfaces.
Within the class `Car`, we use the syntax `Vehicle.super.turnAlarmOff()` to call
the overriden method defined in the parent interface `Vehicle`.  We need to include the
interface name in the method call since there can be multiple parent interfaces.

