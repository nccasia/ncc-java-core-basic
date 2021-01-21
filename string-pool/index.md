# String pool

*source*: https://www.baeldung.com/java-string-pool

## String Interning

When we create a String variable and assign a value to it, the JVM searches the pool for a String of equal value.

**If found, the Java compiler will simply return a reference to its memory address, without allocating additional memory.**

    String constantString1 = "example";
    String constantString2 = "example";
            
    assertThat(constantString1).isSameAs(constantString2);
    
## Strings Allocated Using the Constructor

    String constantString = "example";
    String newString = new String("example");
     
    assertThat(constantString).isNotSameAs(newString);
    
## String Literal vs String Object    

    String first = "example"; 
    String second = "example"; 
    System.out.println(first == second); // True
    
    String third = new String("example");
    String fourth = new String("example"); 
    System.out.println(third == fourth); // False
    
    String fifth = "example";
    String sixth = new String("example");
    System.out.println(fifth == sixth); // False
    
## Manual Interning    

We can manually intern a String in the Java String Pool by calling the intern() method on the object we want to intern.

Manually interning the String will store its reference in the pool, and the JVM will return this reference when needed.

    String constantString = "interned example";
    String newString = new String("interned example");
    
    assertThat(constantString).isNotSameAs(newString);
    
    String internedString = newString.intern();
    
    assertThat(constantString).isSameAs(internedString);        