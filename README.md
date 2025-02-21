## Java-Interview-Questions

When a class implements two interfaces that have the same default method, Java does not know which method to use, and it results in a compilation error unless the class provides its own implementation.

Example Scenario

```java
interface A {
    default void show() {
        System.out.println("A's show method");
    }
}

interface B {
    default void show() {
        System.out.println("B's show method");
    }
}

class MyClass implements A, B {
    public static void main(String[] args) {
        MyClass obj = new MyClass();
        obj.show();  // Compilation Error if not overridden
    }
}
```
Compilation Error:
Duplicate default methods named show with the parameters () are inherited from the types A and B.

How to Resolve This Conflict? To resolve the ambiguity, we must override the conflicting method in MyClass and explicitly specify which interface's method to call using InterfaceName.super.methodName().

```
class MyClass implements A, B {
    @Override
    public void show() {
        // Explicitly calling interface method
        A.super.show();  // Calls A's default method
        // B.super.show(); // If we want to call B's method instead
    }

    public static void main(String[] args) {
        MyClass obj = new MyClass();
        obj.show();  // Prints: A's show method
    }
}
```
## @Transactional Annotation in Spring Boot

The @Transactional annotation in Spring Boot is used to manage database transactions. It ensures that a series of database operations are executed as a single unit of work—either all succeed or all fail (rollback in case of an error).

```java
@Transactional(propagation = Propagation.REQUIRES_NEW)
public void someMethod() {
    // Your code here
}
```

## Garbage Collection (GC) in Java
Garbage Collection (GC) in Java is an automatic memory management process that removes unused objects from heap memory to free up space and avoid memory leaks. It is handled by the JVM (Java Virtual Machine) and does not require explicit deallocation of objects.

## How Does Garbage Collection Work?
Objects are allocated in heap memory when created using new.
Garbage Collector identifies unreachable objects (objects with no active references).
GC removes those objects and reclaims memory.
Memory is compacted and optimized to reduce fragmentation.
When Does Garbage Collection Happen?
The JVM runs GC automatically when it detects low memory.
You can suggest GC using System.gc(), but it is not guaranteed to run immediately.
GC primarily occurs during idle CPU time to minimize performance impact.

## How to Make a Class Immutable in Java?
An immutable class is a class whose objects cannot be modified after creation. All fields of an immutable object remain constant throughout its lifecycle.

Steps to Create an Immutable Class in Java
To make a class immutable, follow these best practices:

1. Declare the class as final
This prevents subclassing, which could allow modification of fields.

final class ImmutableClass {
2. Make all fields private and final
Fields should be private to prevent direct access and final to ensure they are assigned only once.

private final int id;
private final String name;
3. Initialize fields via a constructor
Provide a constructor to initialize all fields.

public ImmutableClass(int id, String name) {
    this.id = id;
    this.name = name;
}
4. Do not provide setter methods
Setters allow modification, so they should not be present.

5. Return deep copies of mutable fields in getters
If a field is a mutable object (like a List or Date), return a defensive copy to prevent modification.

public Date getDate() {
    return new Date(date.getTime());  // Defensive copy
}
## 🔹 Summary Table
Static Feature	Description	Example Usage
1) Static Variable -	Shared by all instances of a class	static int count;
2) Static Method - Belongs to the class, not an instance	static void show();
3) Static Block	- Executes once when the class loads	static { init(); }
4) Static Nested Class	- Does not require an outer class instance	static class Inner {}
5) Static Import - Allows direct use of static members	import static java.lang.Math.*;

## When to Use What?
Use @PathVariable when the value is a key part of the resource path (e.g., /orders/{orderId}).
Use @RequestParam for optional query parameters that refine the request (e.g., /orders?status=shipped).

## Ways to Represent a "Has-a" Relationship in Java:
In object-oriented design, a "has-a" relationship represents composition or aggregation, meaning one class contains an instance (or multiple instances) of another class. This is also known as object association.

# Composition (Strong Association)

If the contained object cannot exist without the container.
Implemented using instance variables.
Example:
```
class Engine {
    void start() {
        System.out.println("Engine started");
    }
}

class Car {
    private Engine engine;  // Strong association (Composition)
    
    public Car() {
        this.engine = new Engine(); // Created inside Car (Car owns Engine)
    }

    void startCar() {
        engine.start();
    }
}

public class Main {
    public static void main(String[] args) {
        Car car = new Car();
        car.startCar();  // Engine started
    }
}
```
Here, Car has-a Engine. If Car is destroyed, its Engine is also destroyed.
# Aggregation (Weak Association)

If the contained object can exist independently.
The parent class holds a reference to the child class instead of owning it.
Example:
```
class Engine {
    void start() {
        System.out.println("Engine started");
    }
}

class Car {
    private Engine engine;  // Weak association (Aggregation)
    
    public Car(Engine engine) {  // Injected from outside
        this.engine = engine;
    }

    void startCar() {
        engine.start();
    }
}

public class Main {
    public static void main(String[] args) {
        Engine engine = new Engine();  // Created independently
        Car car = new Car(engine);    // Passed as a dependency
        car.startCar();  // Engine started
    }
}
```
Here, Engine exists independently, and Car just has a reference to it.
# Summary:
Type	Relationship	Object Lifespan
Composition	Strong association	Contained object depends on container
Aggregation	Weak association	Contained object exists independently

## The wait() and sleep() methods in Java are used to pause the execution of a thread, but they have different use cases and behaviours.

# 1. wait() Method
a) Defined In: java.lang.Object

b) Purpose: Causes the current thread to wait until another thread calls notify() or notifyAll() on the same object.

c) Synchronization: Must be called within a synchronized block or method; otherwise, it throws IllegalMonitorStateException.

d) Releases Lock: Yes, it releases the object's monitor lock while waiting.

e) Wakes Up: When notify() or notifyAll() is called or when interrupted.

Example:
```
class WaitExample {
    public static void main(String[] args) {
        final Object lock = new Object();

        Thread thread1 = new Thread(() -> {
            synchronized (lock) {
                try {
                    System.out.println("Thread 1 waiting...");
                    lock.wait();
                    System.out.println("Thread 1 resumed...");
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        });

        Thread thread2 = new Thread(() -> {
            synchronized (lock) {
                System.out.println("Thread 2 notifying...");
                lock.notify();
            }
        });

        thread1.start();
        try { Thread.sleep(1000); } catch (InterruptedException e) { e.printStackTrace(); }
        thread2.start();
    }
}
Output:
mathematica
Thread 1 waiting...
Thread 2 notifying...
Thread 1 resumed...
```
# 2. sleep() Method
a) Defined In: java.lang.Thread

b) Purpose: Pauses the execution of the current thread for a specified period.

c) Synchronization: Does not require a synchronized block.

d) Releases Lock: No, it holds the lock if inside a synchronized block.

e) Wakes Up: Automatically after the specified time or if interrupted.

Example:
```
class SleepExample {
    public static void main(String[] args) {
        Thread thread = new Thread(() -> {
            try {
                System.out.println("Thread sleeping...");
                Thread.sleep(2000);
                System.out.println("Thread woke up...");
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        });

        thread.start();
    }
}
Output:
mathematica

Thread sleeping...
(Thread pauses for ~2 seconds)
Thread woke up...
```
## Difference Between wait() and sleep()

| Feature               | wait()                                  | sleep()                                 |
|----------------------|--------------------------------------|--------------------------------------|
| **Defined In**       | `java.lang.Object`                   | `java.lang.Thread`                   |
| **Requires Synchronization?** | Yes (`synchronized` block/method) | No |
| **Releases Lock?**   | Yes                                   | No |
| **Wake-Up Condition** | `notify()` / `notifyAll()` or interrupted | After the specified time or interrupted |
| **Thread State**     | `WAITING` or `TIMED_WAITING`         | `TIMED_WAITING`                      |
| **Can Be Called On?** | Any object                           | Only threads (`Thread.sleep()`)      |




# When to Use `wait()` and `sleep()` in Java

Both `wait()` and `sleep()` are used to pause a thread, but their use cases are different. Here's when to use each:

## ✅ Use `wait()` When:

## 1️⃣ Thread Communication is Required  
- If multiple threads are working together and need to wait for some condition to be met before proceeding.  
- **Example**: A producer-consumer problem where the consumer waits until the producer adds items to a queue.

## 2️⃣ Releasing Locks is Necessary  
- If a thread should pause execution but allow other threads to acquire the lock and proceed.  
- **Example**: A thread waiting for a resource to become available.

## 3️⃣ Event-Driven Synchronization is Needed  
- If a thread should only resume when another thread calls `notify()` or `notifyAll()`.  
- **Example**: A thread waiting for user input before proceeding.

## 🔹 Example Usage:
```java
class SharedResource {
    private boolean available = false;

    public synchronized void waitForResource() throws InterruptedException {
        while (!available) {
            wait(); // Releases the lock and waits
        }
        System.out.println("Resource acquired!");
    }

    public synchronized void releaseResource() {
        available = true;
        notify(); // Notifies waiting threads
    }
}
```
### ✅ Use sleep() When:


## 1️⃣ Pausing Execution for a Fixed Time
If a thread needs to be paused for a specific duration without depending on other threads.
Example: A scheduled task that runs every few seconds.

## 2️⃣ CPU Load Reduction
If a thread should pause periodically to avoid high CPU usage.
Example: A polling mechanism that checks for changes every few seconds.

## 3️⃣ Retry Logic Implementation
If a thread needs to retry an operation after a fixed delay.

Example: Retrying a failed API request after a short pause.

🔹 Example Usage:
```
class SleepExample {
    public static void main(String[] args) {
        System.out.println("Task started...");
        try {
            Thread.sleep(2000); // Pauses for 2 seconds
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        System.out.println("Task resumed after sleep...");
    }
}
```


### We can create a thread using two primary approaches:-

Extending the Thread class
Implementing the Runnable interface
Additionally, Java 8 introduced lambda expressions and Java 5 introduced the Executor framework, which are also commonly used to create and manage threads.

# 1️⃣ Creating a Thread by Extending Thread Class

The Thread class provides a run() method that we can override.
We create an instance of the subclass and call start() to begin execution.
🔹 Example:
```
class MyThread extends Thread {
    @Override
    public void run() {
        System.out.println("Thread is running...");
    }
}

public class ThreadExample {
    public static void main(String[] args) {
        MyThread t1 = new MyThread();
        t1.start(); // Starts the thread execution
    }
}
```
🔹 Output:

arduino
Copy
Edit
Thread is running...
✅ Use Case: When we need to override Thread methods or maintain object-oriented behavior.

# 2️⃣ Creating a Thread by Implementing Runnable Interface

Runnable is a functional interface with a single method run().
The Thread class takes a Runnable object in its constructor.
🔹 Example:
```
class MyRunnable implements Runnable {
    @Override
    public void run() {
        System.out.println("Thread is running...");
    }
}

public class RunnableExample {
    public static void main(String[] args) {
        Thread t1 = new Thread(new MyRunnable());
        t1.start();
    }
}
```
🔹 Output:

arduino
Copy
Edit
Thread is running...
✅ Use Case: When we need to implement multiple interfaces (since Java does not support multiple inheritance).

# 3️⃣ Using Java 8 Lambda Expressions

Since Runnable is a functional interface, we can use a lambda expression.
🔹 Example:
```
public class LambdaThreadExample {
    public static void main(String[] args) {
        Thread t1 = new Thread(() -> System.out.println("Thread is running using Lambda..."));
        t1.start();
    }
}
```
🔹 Output:

arduino

Thread is running using Lambda...
✅ Use Case: When we want cleaner and more concise code.

# 4️⃣ Using ExecutorService (Thread Pool - Recommended for Large Scale Apps)
Instead of manually managing threads, Java provides the ExecutorService framework.
It allows efficient thread pooling and resource management.
🔹 Example:
```
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;

public class ExecutorExample {
    public static void main(String[] args) {
        ExecutorService executor = Executors.newFixedThreadPool(2);

        executor.submit(() -> System.out.println("Thread executed using ExecutorService"));
        executor.shutdown(); // Always shut down the executor after use
    }
}
```
🔹 Output:

Thread executed using ExecutorService
✅ Use Case: When we need better thread management and scalability.

# 🔥 Summary Table
Approach	Pros	Cons
Extending Thread	Simple to use	Can't extend another class
Implementing Runnable	Supports multiple inheritance	Requires Thread class to run
Lambda Expression	More concise, readable	Only works for Runnable
Executor Framework	Best for large-scale applications	Slightly complex to implement
## 🚀 Which One Should You Use?
✅ Use Runnable or Lambda when you don’t need to extend Thread.
✅ Use ExecutorService for multi-threading in real-world applications.
❌ Avoid directly extending Thread, unless necessary.







You said:
when should we implement Runnable and when should we extend Thread class
ChatGPT said:
✅ When to Implement Runnable vs. Extend Thread in Java?
In Java, you can create a thread by implementing Runnable or extending Thread, but the best choice depends on the use case.

🔹 Implement Runnable When:
✅ You need to follow best practices – Implementing Runnable is the preferred approach.
✅ You need multiple inheritance – Since Java doesn’t support multiple inheritance, you can extend another class while implementing Runnable.
✅ You want to separate logic from threading behavior – The Runnable object can be passed to different Thread instances.
✅ You want better thread management – Works well with thread pools (ExecutorService).

🔹 Example:
java
Copy
Edit
class MyTask implements Runnable {
    @Override
    public void run() {
        System.out.println("Runnable thread is running...");
    }
}

public class RunnableExample {
    public static void main(String[] args) {
        Thread t1 = new Thread(new MyTask()); // Pass Runnable to Thread
        t1.start();
    }
}
📝 Use Case:

When your class already extends another class, since Java doesn’t support multiple inheritance.

When working with ExecutorService for better thread management.

🔹 Extend Thread When:
✅ You need to override Thread class methods – If you need to modify methods like start(), run(), or interrupt().
✅ Your thread has unique behavior – If the thread logic is tightly coupled with the thread itself.
✅ You don’t need multiple inheritance – Since extending Thread means you can't extend another class.

🔹 Example:
```

class MyThread extends Thread {
    @Override
    public void run() {
        System.out.println("Thread class is running...");
    }
}

public class ThreadExample {
    public static void main(String[] args) {
        MyThread t1 = new MyThread();
        t1.start();
    }
}
```
📝 Use Case:

When you need full control over the thread behavior.
When extending Thread makes the code more readable (not recommended for large applications).
# 🔥 Key Differences:=
Feature	Implementing Runnable	Extending Thread
Inheritance	Allows extending another class	Cannot extend another class
Code Reusability	Better, as Runnable can be reused	Less reusable, as logic is in Thread
Flexibility	Can be used with ExecutorService	Cannot be used with ExecutorService
Coupling	Loosely coupled with Thread	Tightly coupled with Thread
# 🚀 Final Recommendation:

Use Runnable in most cases – It promotes code reusability and allows multiple inheritance.

Extend Thread only when you need to modify thread behavior (e.g., overriding start() or interrupt()).

For large-scale applications, always use Runnable with ExecutorService.

# ✅ When to Use ConcurrentHashMap vs. HashMap in Java?

Both ConcurrentHashMap and HashMap store key-value pairs, but their use cases differ based on thread safety and performance requirements.

🔹 Use HashMap When:

✅ Single-threaded environments – If your application doesn’t have multiple threads modifying the map concurrently.

✅ Performance is a priority – HashMap is faster than ConcurrentHashMap because it has no synchronization overhead.

✅ You don’t need thread safety – If your map is accessed by only one thread at a time.

🔹 Example:
```
import java.util.HashMap;

public class HashMapExample {
    public static void main(String[] args) {
        HashMap<Integer, String> map = new HashMap<>();
        map.put(1, "Apple");
        map.put(2, "Banana");
        System.out.println(map.get(1)); // Output: Apple
    }
}
```

🚫 Avoid using HashMap in multi-threaded environments – If multiple threads modify it, it may cause data inconsistency or even a race condition.

🔹 Use ConcurrentHashMap When:

✅ Multi-threaded environments – If multiple threads read and write to the map simultaneously.

✅ Thread safety is required – Unlike HashMap, ConcurrentHashMap doesn’t allow structural modifications (e.g., resizing) to cause issues in concurrent environments.

✅ Better performance than Collections.synchronizedMap() – It uses finer-grained locking, allowing better performance in concurrent scenarios.

🔹 Example:
```
import java.util.concurrent.ConcurrentHashMap;

public class ConcurrentHashMapExample {
    public static void main(String[] args) {
        ConcurrentHashMap<Integer, String> map = new ConcurrentHashMap<>();
        map.put(1, "Apple");
        map.put(2, "Banana");

        System.out.println(map.get(1)); // Output: Apple
    }
}
```





