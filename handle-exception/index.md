# Handle exception

*source*: https://www.baeldung.com/java-exceptions

## Handling Exceptions

    /**
     * @exception FileNotFoundException ...
     */
    public Scanner(String fileName) throws FileNotFoundException {
       // ...
    }
    
### throws

The simplest way to “handle” an exception is to rethrow it:

    public int getPlayerScore(String playerFile)
      throws FileNotFoundException {
     
        Scanner contents = new Scanner(new File(playerFile));
        return Integer.parseInt(contents.nextLine());
    }
    
Because FileNotFoundException is a checked exception, this is the simplest way to satisfy the compiler, but it does mean that anyone that calls our method now needs to handle it too!

parseInt can throw a NumberFormatException, but because it is unchecked, we aren't required to handle it.

### try-catch

    public int getPlayerScore(String playerFile) {
        try {
            Scanner contents = new Scanner(new File(playerFile));
            return Integer.parseInt(contents.nextLine());
        } catch (FileNotFoundException noFile) {
            throw new IllegalArgumentException("File not found");
        }
    }
    
    public int getPlayerScore(String playerFile) {
        try {
            Scanner contents = new Scanner(new File(playerFile));
            return Integer.parseInt(contents.nextLine());
        } catch ( FileNotFoundException noFile ) {
            logger.warn("File not found, resetting score.");
            return 0;
        }
    }
    
### finally

    public int getPlayerScore(String playerFile)
      throws FileNotFoundException {
        Scanner contents = null;
        try {
            contents = new Scanner(new File(playerFile));
            return Integer.parseInt(contents.nextLine());
        } finally {
            if (contents != null) {
                contents.close();
            }
        }
    }
    
Here, the finally block indicates what code we want Java to run regardless of what happens with trying to read the file.

Even if a FileNotFoundException is thrown up the call stack, Java will call the contents of finally before doing that.

We can also both handle the exception and make sure that our resources get closed:

    public int getPlayerScore(String playerFile) {
        Scanner contents;
        try {
            contents = new Scanner(new File(playerFile));
            return Integer.parseInt(contents.nextLine());
        } catch (FileNotFoundException noFile ) {
            logger.warn("File not found, resetting score.");
            return 0; 
        } finally {
            try {
                if (contents != null) {
                    contents.close();
                }
            } catch (IOException io) {
                logger.error("Couldn't close the reader!", io);
            }
        }
    }
    
### try-with-resources

    public int getPlayerScore(String playerFile) {
        try (Scanner contents = new Scanner(new File(playerFile))) {
          return Integer.parseInt(contents.nextLine());
        } catch (FileNotFoundException e ) {
          logger.warn("File not found, resetting score.");
          return 0;
        }
    }
    
When we place references that are AutoClosable in the try declaration, then we don't need to close the resource ourselves.

We can still use a finally block, though, to do any other kind of cleanup we want.

### Multiple catch Blocks

    public int getPlayerScore(String playerFile) {
        try (Scanner contents = new Scanner(new File(playerFile))) {
            return Integer.parseInt(contents.nextLine());
        } catch (IOException e) {
            logger.warn("Player file wouldn't load!", e);
            return 0;
        } catch (NumberFormatException e) {
            logger.warn("Player file was corrupted!", e);
            return 0;
        }
    }
    
Also note here that we didn't catch FileNotFoundException, and that is because it extends IOException. Because we're catching IOException, Java will consider any of its subclasses also handled.

Let's say, though, that we need to treat FileNotFoundException differently from the more general IOException:

    public int getPlayerScore(String playerFile) {
        try (Scanner contents = new Scanner(new File(playerFile)) ) {
            return Integer.parseInt(contents.nextLine());
        } catch (FileNotFoundException e) {
            logger.warn("Player file not found!", e);
            return 0;
        } catch (IOException e) {
            logger.warn("Player file wouldn't load!", e);
            return 0;
        } catch (NumberFormatException e) {
            logger.warn("Player file was corrupted!", e);
            return 0;
        }
    }
    
### Union catch Blocks

    public int getPlayerScore(String playerFile) {
        try (Scanner contents = new Scanner(new File(playerFile))) {
            return Integer.parseInt(contents.nextLine());
        } catch (IOException | NumberFormatException e) {
            logger.warn("Failed to load score!", e);
            return 0;
        }
    }
    
## Throwing Exceptions

If we don't want to handle the exception ourselves or we want to generate our exceptions for others to handle, then we need to get familiar with the throw keyword.

Let's say that we have the following checked exception we've created ourselves:

    public class TimeoutException extends Exception {
        public TimeoutException(String message) {
            super(message);
        }
    }
    
and we have a method that could potentially take a long time to complete:

    public List<Player> loadAllPlayers(String playersFile) {
        // ... potentially long operation
    }
    
### Throwing a Checked Exception

    public List<Player> loadAllPlayers(String playersFile) throws TimeoutException {
        while ( !tooLong ) {
            // ... potentially long operation
        }
        throw new TimeoutException("This operation took too long");
    }
    
### Throwing an Unchecked Exception

If we want to do something like, say, validate input, we can use an unchecked exception instead:

    public List<Player> loadAllPlayers(String playersFile) throws TimeoutException {
        if(!isFilenameValid(playersFile)) {
            throw new IllegalArgumentException("Filename isn't valid!");
        }
       
        // ...
    }
    
Because IllegalArgumentException is unchecked, we don't have to mark the method, though we are welcome to.

### Wrapping and Rethrowing

We can also choose to rethrow an exception we've caught:
    
    public List<Player> loadAllPlayers(String playersFile) 
      throws IOException {
        try { 
            // ...
        } catch (IOException io) { 		
            throw io;
        }
    }
    
Or do a wrap and rethrow:

    public List<Player> loadAllPlayers(String playersFile) 
      throws PlayerLoadException {
        try { 
            // ...
        } catch (IOException io) { 		
            throw new PlayerLoadException(io);
        }
    }
    
## Anti-Patterns

### Swallowing Exceptions

    public int getPlayerScore(String playerFile) {
        try {
            // ...
        } catch (Exception e) {} // <== catch and swallow
        return 0;
    }
    
    public int getPlayerScore(String playerFile) {
        try {
            // ...
        } catch (IOException e) {
            // this will never happen
        }
    }
    
Another way we can “swallow” an exception is to print out the exception to the error stream simply:

    public int getPlayerScore(String playerFile) {
        try {
            // ...
        } catch (Exception e) {
            e.printStackTrace();
        }
        return 0;
    }
    
We've improved our situation a bit by a least writing the error out somewhere for later diagnosis.

It'd be better, though, for us to use a logger:

    public int getPlayerScore(String playerFile) {
        try {
            // ...
        } catch (IOException e) {
            logger.error("Couldn't load the score", e);
            return 0;
        }
    }
    
Finally, we can inadvertently swallow an exception by not including it as a cause when we are throwing a new exception:

    public int getPlayerScore(String playerFile) {
        try {
            // ...
        } catch (IOException e) {
            throw new PlayerScoreException();
        }
    }
    
Here, we pat ourselves on the back for alerting our caller to an error, but we fail to include the IOException as the cause. Because of this, we've lost important information that callers or operators could use to diagnose the problem.

We'd be better off doing:

    public int getPlayerScore(String playerFile) {
        try {
            // ...
        } catch (IOException e) {
            throw new PlayerScoreException(e);
        }
    }
    
### Using return in a finally Block

Another way to swallow exceptions is to return from the finally block. This is bad because, by returning abruptly, the JVM will drop the exception, even if it was thrown from by our code:

    public int getPlayerScore(String playerFile) {
        int score = 0;
        try {
            throw new IOException();
        } finally {
            return score; // <== the IOException is dropped
        }
    }
    
### Using throw in a finally Block

Similar to using return in a finally block, the exception thrown in a finally block will take precedence over the exception that arises in the catch block.

This will “erase” the original exception from the try block, and we lose all of that valuable information:

    public int getPlayerScore(String playerFile) {
        try {
            // ...
        } catch ( IOException io ) {
            throw new IllegalStateException(io); // <== eaten by the finally
        } finally {
            throw new OtherException();
        }
    }
    
### Using throw as a goto

Some people also gave into the temptation of using throw as a goto statement:

    public void doSomething() {
        try {
            // bunch of code
            throw new MyException();
            // second bunch of code
        } catch (MyException e) {
            // third bunch of code
        }		
    }
    
## Common Exceptions and Errors

### Checked Exceptions

IOException – This exception is typically a way to say that something on the network, filesystem, or database failed.

### RuntimeExceptions

* ArrayIndexOutOfBoundsException – this exception means that we tried to access a non-existent array index, like when trying to get index 5 from an array of length 3.
* ClassCastException – this exception means that we tried to perform an illegal cast, like trying to convert a String into a List. We can usually avoid it by performing defensive instanceof checks before casting.
* IllegalArgumentException – this exception is a generic way for us to say that one of the provided method or constructor parameters is invalid.
* IllegalStateException – This exception is a generic way for us to say that our internal state, like the state of our object, is invalid.
* NullPointerException – This exception means we tried to reference a null object. We can usually avoid it by either performing defensive null checks or by using Optional.
* NumberFormatException – This exception means that we tried to convert a String into a number, but the string contained illegal characters, like trying to convert “5f3” into a number.

### Errors

* StackOverflowError – this exception means that the stack trace is too big. This can sometimes happen in massive applications; however, it usually means that we have some infinite recursion happening in our code.
* NoClassDefFoundError – this exception means that a class failed to load either due to not being on the classpath or due to failure in static initialization.
* OutOfMemoryError –  this exception means that the JVM doesn't have any more memory available to allocate for more objects. Sometimes, this is due to a memory leak.                            
                                
            
    
    
                                                                                     