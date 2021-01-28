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