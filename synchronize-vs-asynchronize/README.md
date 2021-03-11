# Synchronize vs Asynchronize

*source*: https://stackabuse.com/synchronized-keyword-in-java/

## Synchronized Methods

Defining synchronized methods is as easy as simply including the keyword before the return type. Let's define a method that prints out the numbers between 1 and 5 in a sequential manner.

    public class NonSynchronizedMethod {
    
        public void printNumbers() {
            System.out.println("Starting to print Numbers for " + Thread.currentThread().getName());
    
            for (int i = 0; i < 5; i++) {
                System.out.println(Thread.currentThread().getName() + " " + i);
            }
    
            System.out.println("Completed printing Numbers for " + Thread.currentThread().getName());
        }
    }
    
Now, let's implement two custom threads that access this object and wish to run the printNumbers() method:

    class ThreadOne extends Thread {
    
        NonSynchronizedMethod nonSynchronizedMethod;
    
        public ThreadOne(NonSynchronizedMethod nonSynchronizedMethod) {
            this.nonSynchronizedMethod = nonSynchronizedMethod;
        }
    
        @Override
        public void run() {
            nonSynchronizedMethod.printNumbers();
        }
    }
    
    class ThreadTwo extends Thread {
    
        NonSynchronizedMethod nonSynchronizedMethod;
    
        public ThreadTwo(NonSynchronizedMethod nonSynchronizedMethod) {
            this.nonSynchronizedMethod = nonSynchronizedMethod;
        }
    
        @Override
        public void run() {
            nonSynchronizedMethod.printNumbers();
        }
    }
    
These threads share a common object NonSynchronizedMethod and they will simultaneously try to call the non-synchronized method printNumbers() on this object.

To test this behavior, let's write a main class:

    public class TestSynchronization {
        public static void main(String[] args) {
    
            NonSynchronizedMethod nonSynchronizedMethod = new NonSynchronizedMethod();
    
            ThreadOne threadOne = new ThreadOne(nonSynchronizedMethod);
            threadOne.setName("ThreadOne");
    
            ThreadTwo threadTwo = new ThreadTwo(nonSynchronizedMethod);
            threadTwo.setName("ThreadTwo");
    
            threadOne.start();
            threadTwo.start();
    
        }
    }
    
Running the code will give us something along the lines of:

    Starting to print Numbers for ThreadOne
    Starting to print Numbers for ThreadTwo
    ThreadTwo 0
    ThreadTwo 1
    ThreadTwo 2
    ThreadTwo 3
    ThreadTwo 4
    Completed printing Numbers for ThreadTwo
    ThreadOne 0
    ThreadOne 1
    ThreadOne 2
    ThreadOne 3
    ThreadOne 4
    Completed printing Numbers for ThreadOne
    
Now, let's adequately synchronize our method:

    public synchronized void printNumbers() {
        System.out.println("Starting to print Numbers for " + Thread.currentThread().getName());
    
        for (int i = 0; i < 5; i++) {
            System.out.println(Thread.currentThread().getName() + " " + i);
        }
    
        System.out.println("Completed printing Numbers for " + Thread.currentThread().getName());
    }
    
Absolutely nothing has changed, besides including the synchronized keyword. Now, when we run the code:

    Starting to print Numbers for ThreadOne
    ThreadOne 0
    ThreadOne 1
    ThreadOne 2
    ThreadOne 3
    ThreadOne 4
    Completed printing Numbers for ThreadOne
    Starting to print Numbers for ThreadTwo
    ThreadTwo 0
    ThreadTwo 1
    ThreadTwo 2
    ThreadTwo 3
    ThreadTwo 4
    Completed printing Numbers for ThreadTwo
    
## Synchronized Blocks    

The main aim of multi-threading is to execute as many tasks in parallel as is possible. However, synchronization throttles the parallelism for threads which have to execute synchronized method or block.

    public class SynchronizedBlockExample {
    
        public void printNumbers() {
    
            System.out.println("Starting to print Numbers for " + Thread.currentThread().getName());
    
            synchronized (this) {
                for (int i = 0; i < 5; i++) {
                    System.out.println(Thread.currentThread().getName() + " " + i);
                }
            }
    
            System.out.println("Completed printing Numbers for " + Thread.currentThread().getName());
        }
    }
    
Let's check out the output now:

    Starting to print Numbers for ThreadOne
    Starting to print Numbers for ThreadTwo
    ThreadOne 0
    ThreadOne 1
    ThreadOne 2
    ThreadOne 3
    ThreadOne 4
    Completed printing Numbers for ThreadOne
    ThreadTwo 0
    ThreadTwo 1
    ThreadTwo 2
    ThreadTwo 3
    ThreadTwo 4
    Completed printing Numbers for ThreadTwo
    
# Asynchronous and Synchronous Callbacks in Java
*source: https://www.geeksforgeeks.org/asynchronous-synchronous-callbacks-java/*    

A CallBack Function is a function that is passed into another function as an argument and is expected to execute after some kind of event. The purpose of the callback function is to inform a class Sync/Async if some work in another class is done. This is very useful when working with Asynchronous tasks. Suppose we want to perform some routine tasks like perform some operation or display content after clicking a button, or fetching data from internet. This is also used in event handling, as we get notified when a button is clicked via callback function.

This type of design pattern is used in **Observer Design Pattern**.The observer pattern is a software design pattern in which an object, called the subject, maintains a list of its dependent, called observers, and notifies them automatically of any state changes, usually by calling one of their methods( Source:wiki).

In Java, Callbacks can be implemented using an interface. The general procedure for implementation is given below.

    1. Define the methods in an interface that we want to invoke after callback.
    2. Define a class that will implement the callback methods of the interface.
    3. Define a reference in other class to register the callback interface.
    4. Use that reference to invoke the callback method.
    
## Synchronous Callback

The code execution will block or wait for the event before continuing. Until your event returns a response, your program will not execute any further. So Basically, the callback performs all its work before returning to the call statement. The problem with synchronous callbacks are that they appear to lag.

    // Java program to illustrate synchronous callback 
    interface OnGeekEventListener { 
      
        // this can be any type of method 
        void onGeekEvent(); 
    } 
      
    class B { 
      
        private OnGeekEventListener mListener; // listener field 
      
        // setting the listener 
        public void registerOnGeekEventListener(OnGeekEventListener mListener) 
        { 
            this.mListener = mListener; 
        } 
      
        // my synchronous task 
        public void doGeekStuff() 
        { 
      
            // perform any operation 
            System.out.println("Performing callback before synchronous Task"); 
      
            // check if listener is registered. 
            if (this.mListener != null) { 
      
                // invoke the callback method of class A 
                mListener.onGeekEvent(); 
            } 
        } 
      
        // Driver Function 
        public static void main(String[] args) 
        { 
            B obj = new B(); 
            OnGeekEventListener mListener = new A(); 
            obj.registerOnGeekEventListener(mListener); 
            obj.doGeekStuff(); 
        } 
    } 
      
    class A implements OnGeekEventListener { 
      
        @Override
        public void onGeekEvent() 
        { 
            System.out.println("Performing callback after synchronous Task"); 
            // perform some routine operation 
        } 
        // some class A methods 
    }
    
Output:

    Performing callback before synchronous Task
    Performing callback after synchronous Task
    
## Asynchronous Callback

An Asynchronous call does not block the program from the code execution. When the call returns from the event, the call returns back to the callback function. So in the context of Java, we have to Create a new thread and invoke the callback method inside that thread. The callback function may be invoked from a thread but is not a requirement. A Callback may also start a new thread, thus making themselves asynchronous.    

    // Java program to illustrate Asynchronous callback 
      
    interface OnGeekEventListener { 
      
        // this can be any type of method 
        void onGeekEvent(); 
    } 
      
    class B { 
      
        private OnGeekEventListener mListener; // listener field 
      
        // setting the listener 
        public void registerOnGeekEventListener(OnGeekEventListener mListener) 
        { 
            this.mListener = mListener; 
        } 
      
        // My Asynchronous task 
        public void doGeekStuff() 
        { 
      
            // An Async task always executes in new thread 
            new Thread(new Runnable() { 
                public void run() 
                { 
      
                    // perform any operation 
                    System.out.println("Performing operation in Asynchronous Task"); 
      
                    // check if listener is registered. 
                    if (mListener != null) { 
      
                        // invoke the callback method of class A 
                        mListener.onGeekEvent(); 
                    } 
                } 
            }).start(); 
        } 
      
        // Driver Program 
        public static void main(String[] args) 
        { 
      
            B obj = new B(); 
            OnGeekEventListener mListener = new A(); 
            obj.registerOnGeekEventListener(mListener); 
            obj.doGeekStuff(); 
        } 
    } 
      
    class A implements OnGeekEventListener { 
      
        @Override
        public void onGeekEvent() 
        { 
            System.out.println("Performing callback after Asynchronous Task"); 
            // perform some routine operation 
        } 
        // some class A methods 
    } 
    
Output:

    Performing operation in Asynchronous Task
    Performing callback after Asynchronous Task    
    
## When To Use What    

**Synchronous Callback** : Any process having multiple tasks where the tasks must be executed in sequence and doesn’t occupy much time should use synchronous Callbacks.
For example : You’re in a movie queue for ticket you can’t get one until everyone in front of you gets one.

**Asynchronous Callback** : When the tasks are not dependent on each other and may take some time for execution we should use Asynchronous callbacks.
For example : When you order your food other people can also order their food in the restaurant. They don’t have to wait for your order to finish, If you’re downloading a song from internet, Getting an API response.