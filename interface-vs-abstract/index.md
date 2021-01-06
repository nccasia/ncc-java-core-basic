# Interface vs Abstract class

## Abstract class
*source*: https://www.journaldev.com/1582/abstract-class-in-java 
 
Abstract class in Java is similar to interface except that it can contain default method implementation. An abstract class can have an abstract method without body and it can have methods with implementation also.

**abstract** keyword is used to create a abstract class and method. Abstract class in java can’t be instantiated. An abstract class is mostly used to provide a base for subclasses to extend and implement the abstract methods and override or use the implemented methods in abstract class.

### Important points

1. **abstract** keyword is used to create an abstract class in java.
2. Abstract class in java can’t be instantiated.
3. We can use **abstract** keyword to create an abstract method, an abstract method doesn’t have body.
4. If a class have abstract methods, then the class should also be abstract using abstract keyword, else it will not compile.
5. It’s not necessary for an abstract class to have abstract method. We can mark a class as abstract even if it doesn’t declare any abstract methods.
6. If abstract class doesn’t have any method implementation, its better to use interface because java doesn’t support multiple class inheritance.
7. The subclass of abstract class in java must implement all the abstract methods unless the subclass is also an abstract class.
8. All the methods in an interface are implicitly abstract unless the interface methods are static or default. Static methods and default methods in interfaces are added in Java 8, for more details read Java 8 interface changes.
9. Java Abstract class can implement interfaces without even providing the implementation of interface methods.
10. Java Abstract class is used to provide common method implementation to all the subclasses or to provide default implementation.
11. We can run abstract class in java like any other class if it has **main()** method.

        package com.journaldev.design;
        
        //abstract class
        public abstract class Person {
        	
        	private String name;
        	private String gender;
        	
        	public Person(String nm, String gen){
        		this.name=nm;
        		this.gender=gen;
        	}
        	
        	//abstract method
        	public abstract void work();
        	
        	@Override
        	public String toString(){
        		return "Name="+this.name+"::Gender="+this.gender;
        	}
        
        	public void changeName(String newName) {
        		this.name = newName;
        	}	
        }


        package com.journaldev.design;
        
        public class Employee extends Person {
        	
        	private int empId;
        	
        	public Employee(String nm, String gen, int id) {
        		super(nm, gen);
        		this.empId=id;
        	}
        
        	@Override
        	public void work() {
        		if(empId == 0){
        			System.out.println("Not working");
        		}else{
        			System.out.println("Working as employee!!");
        		}
        	}
        	
        	public static void main(String args[]){
        		//coding in terms of abstract classes
        		Person student = new Employee("Dove","Female",0);
        		Person employee = new Employee("Pankaj","Male",123);
        		student.work();
        		employee.work();
        		//using method implemented in abstract class - inheritance
        		employee.changeName("Pankaj Kumar");
        		System.out.println(employee.toString());
        	}
        
        }

## Interface
*source*: https://www.journaldev.com/1601/interface-in-java

Interface in java provide a way to achieve abstraction. Java interface is also used to define the contract for the subclasses to implement.

### Important points

1. interface is the code that is used to create an interface in java.
2. We can’t instantiate an interface in java.
3. Interface provides absolute abstraction, in last post we learned about abstract classes in java to provide abstraction but abstract classes can have method implementations but interface can’t.
4. Interfaces can’t have constructors because we can’t instantiate them and interfaces can’t have a method with body.
5. By default any attribute of interface is public, static and final, so we don’t need to provide access modifiers to the attributes but if we do, compiler doesn’t complain about it either.
6. By default interface methods are implicitly abstract and public, it makes total sense because the method don’t have body and so that subclasses can provide the method implementation.
7. An interface can’t extend any class but it can extend another interface. public interface Shape extends Cloneable{} is an example of an interface extending another interface. Actually java provides multiple inheritance in interfaces, what is means is that an interface can extend multiple interfaces.
8. implements keyword is used by classes to implement an interface.
9. A class implementing an interface must provide implementation for all of its method unless it’s an abstract class
10. We should always try to write programs in terms of interfaces rather than implementations so that we know beforehand that implementation classes will always provide the implementation and in future if any better implementation arrives, we can switch to that easily.

        package com.journaldev.design;
        
        public interface Shape {
        
        	//implicitly public, static and final
        	public String LABLE="Shape";
        	
        	//interface methods are implicitly abstract and public
        	void draw();
        	
        	double getArea();
        }
        
### Java Interface Benefits

1. Interface provides a contract for all the implementation classes, so its good to code in terms of interfaces because implementation classes can’t remove the methods we are using.
2. Interfaces are good for starting point to define Type and create top level hierarchy in our code.
3. Since a java class can implements multiple interfaces, it’s better to use interfaces as super class in most of the cases.

### Java Interface Disadvantages

1. We need to chose interface methods very carefully at the time of designing our project because we can’t add of remove any methods from the interface at later point of time, it will lead compilation error for all the implementation classes. Sometimes this leads to have a lot of interfaces extending the base interface in our code that becomes hard to maintain.
2. If the implementation classes has its own methods, we can’t use them directly in our code because the type of Object is an interface that doesn’t have those methods. For example, in above code we will get compilation error for code shape.getRadius(). To overcome this, we can use typecasting and use the method like this:
    
        Circle c = (Circle) shape;
        c.getRadius();
        
## Difference Between Interface and Abstract Class
*source*: 
*   https://javapapers.com/core-java/abstract-and-interface-core-java-2/difference-between-a-java-interface-and-a-java-abstract-class/
*   https://www.geeksforgeeks.org/difference-between-abstract-class-and-interface-in-java/
*   https://www.javatpoint.com/difference-between-abstract-class-and-interface
*   https://www.guru99.com/interface-vs-abstract-class-java.html



1. Main difference is methods of a Java interface are implicitly abstract and cannot have implementations. A Java abstract class can have instance methods that implements a default behavior.
2. Variables declared in a Java interface is by default final. An  abstract class may contain non-final variables.
3. Members of a Java interface are public by default. A Java abstract class can have the usual flavors of class members like private, protected, etc..
4. Java interface should be implemented using keyword “implements”; A Java abstract class should be extended using keyword “extends”.
5. An interface can extend another Java interface only, an abstract class can extend another Java class and implement multiple Java interfaces.
6. A Java class can implement multiple interfaces but it can extend only one abstract class.
7. Interface is absolutely abstract and cannot be instantiated; A Java abstract class also cannot be instantiated, but can be invoked if a main() exists.
8. In comparison with java abstract classes, java interfaces are slow as it requires extra indirection.