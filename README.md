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

![product uml](https://curriculum-content.s3.amazonaws.com/6677/pillars/product_uml.png)

As a simple example, consider the `Product` class:

```java
public class Product {
    public String getName() { return "Widget";}
    public double getPrice() { return 75.0;}
}
```

We'll create a subclass `DiscountProduct` that overrides both methods
by invoking the methods of the superclass `Product`:

```java
public class DiscountProduct extends Product {
    @Override
    public String getName() {
        return "Discount " + super.getName();   // Discount Widget
    }

    @Override
    public double getPrice() {
        return super.getPrice() * 0.90;        //75.0 * 0.9
    }
}
```

The `main()` method creates `Product` and `DiscountProduct` objects and calls their methods:

```java
public class Main {
    public static void main(String[] args) {
        Product product1 = new Product();
        System.out.println(product1.getName() + " costs $" + product1.getPrice());

        DiscountProduct product2 = new DiscountProduct();
        System.out.println(product2.getName() + " costs $" + product2.getPrice());
    }
}
```

The program prints:

```text
Widget costs $75.0
Discount Widget costs $67.5
```

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

