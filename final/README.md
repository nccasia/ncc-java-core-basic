# Final keyword

*source*: https://www.geeksforgeeks.org/final-vs-immutability-java

## Final vs Immutability

### Final

In Java, final is a modifier which is used for class, method and variable also. When a variable is declared with final keyword, it’s value can’t be modified, essentially, a constant.
 
### Immutability

In simple terms, immutability means unchanging over time or unable to be changed. In Java, we know that String objects are immutable means we cant change anything to the existing String objects.

### Differences between final and immutability

*   final means that you can’t change the object’s reference to point to another reference or another object, but you can still mutate its state (using setter methods e.g). Whereas immutable means that the object’s actual value can’t be changed, but you can change its reference to another one.
*   final modifier is applicable for variable but not for objects, Whereas immutability applicable for an object but not for variables.
*   By declaring a reference variable as final, we won’t get any immutability nature, Even though reference variable is final. We can perform any type of change in the corresponding Object. But we cant perform reassignment for that variable.
*   final ensures that the address of the object remains the same whereas the Immutable suggests that we can’t change the state of the object once created. 

*source*: https://www.baeldung.com/java-final

## Final Classes

Classes marked as final can’t be extended.

    public final class Cat {
    
        private int weight;
    
        // standard getter and setter
    }
    
And let’s try to extend it:

    public class BlackCat extends Cat {
    }
    
We’ll see the compiler error:
    
    The type BlackCat cannot subclass the final class Cat
    
Note that **the final keyword in a class declaration doesn’t mean that the objects of this class are immutable**. We can change the fields of Cat object freely:

    Cat cat = new Cat();
    cat.setWeight(1);
    
    assertEquals(1, cat.getWeight());
    
## Final Methods

Methods marked as final cannot be overridden.

## Final Variables

**Variables marked as final can't be reassigned**. Once a final variable is initialized, it can’t be altered.

### Final Primitive Variables

        public void whenFinalVariableAssign_thenOnlyOnce() {
            final int i = 1;
            //...
            i=2;
        }

The compiler says:
    
    The final local variable i may already have been assigned
    
### Final Reference Variables

If we have a final reference variable, we can’t reassign it either. **But this doesn’t mean that the object it refers to is immutable**. We can change the properties of this object freely.

    final Cat cat = new Cat();
    
If we try to reassign it we’ll see a compiler error:

    The final local variable cat cannot be assigned. It must be blank and not using a compound assignment
    
But we can change the properties of Cat instance:

    cat.setWeight(5);
    
    assertEquals(5, cat.getWeight());
    
### Final Fields

Final fields can be either constants or write-once fields.

Note that according to naming conventions, class constants should be uppercase, with components separated by underscore (“_”) characters:

    static final int MAX_WIDTH = 999;
    
Note that any final field must be initialized before the constructor completes.

### Final Arguments

A final argument can’t be changed inside a method:

    public void methodWithFinalArguments(final int x) {
        x=1;
    }
    
The above assignment causes the compiler error:

    The final local variable x cannot be assigned. It must be blank and not using a compound assignment        
                                