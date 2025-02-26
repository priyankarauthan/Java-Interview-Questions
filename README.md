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





###  When to Implement Runnable vs. Extend Thread in Java?

In Java, you can create a thread by implementing Runnable or extending Thread, but the best choice depends on the use case.

# 🔹 Implement Runnable When:
✅ You need to follow best practices – Implementing Runnable is the preferred approach.
✅ You need multiple inheritance – Since Java doesn’t support multiple inheritance, you can extend another class while implementing Runnable.
✅ You want to separate logic from threading behavior – The Runnable object can be passed to different Thread instances.
✅ You want better thread management – Works well with thread pools (ExecutorService).

🔹 Example:

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

# 🔹 Extend Thread When:

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

#### ✅ What is `Callable` in Java?  

`Callable<T>` is an interface in Java used to represent **tasks that return a result and can throw checked exceptions**. It is similar to `Runnable`, but with **more capabilities**.  

---

## 🔹 Key Differences Between `Callable` and `Runnable`  

| **Feature**               | **Runnable**                          | **Callable<T>**                        |
|---------------------------|--------------------------------------|----------------------------------------|
| **Return Type**           | `void` (does not return a value)     | Returns a value (`T`)                 |
| **Exception Handling**    | Cannot throw checked exceptions      | Can throw checked exceptions          |
| **Used With**             | `Thread` or `ExecutorService`        | `ExecutorService` with `Future<T>`    |
| **Blocking Behavior**     | Does not support blocking            | Supports blocking with `Future.get()` |

---


## 🔹 Why Use Callable?
✅ When you need to return a result from a thread.
✅ When you need to handle checked exceptions in concurrent tasks.
✅ When working with thread pools (ExecutorService).

###  ✅ Deadlock in Java Multi-Threading

A deadlock occurs in Java when two or more threads are waiting for each other to release locks, but none of them can proceed, causing a permanent block in execution.

### 🔹 How Does Deadlock Happen?
A deadlock situation can occur when:

Thread-1 holds Lock-A and waits for Lock-B.
Thread-2 holds Lock-B and waits for Lock-A.
Neither thread can proceed because both are waiting for each other to release the locks.

### 🛠 How to Prevent Deadlock?

✅ 1. Use Lock Ordering
Always acquire locks in the same order to avoid circular waiting.
```
class SafeResource {
    void methodA(SafeResource resource) {
        synchronized (this) { // Always acquire locks in order
            System.out.println(Thread.currentThread().getName() + " locked " + this);
            synchronized (resource) {
                System.out.println(Thread.currentThread().getName() + " locked " + resource);
            }
        }
    }
}
```
✅ 2. Use tryLock() Instead of synchronized (Avoid Waiting Forever)
Using ReentrantLock.tryLock() prevents deadlock by timing out if a lock isn’t available.
```
import java.util.concurrent.locks.Lock;
import java.util.concurrent.locks.ReentrantLock;

class SafeResource {
    private final Lock lock = new ReentrantLock();

    void methodA(SafeResource resource) {
        try {
            if (lock.tryLock()) {
                try {
                    System.out.println(Thread.currentThread().getName() + " locked " + this);
                    if (resource.lock.tryLock()) {
                        try {
                            System.out.println(Thread.currentThread().getName() + " locked " + resource);
                        } finally {
                            resource.lock.unlock();
                        }
                    }
                } finally {
                    lock.unlock();
                }
            }
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}
```
✅ 3. Avoid Nested Locks

Minimize synchronized blocks and use locks only when needed.
```
synchronized (lock1) {
    // Critical section
}
synchronized (lock2) {  // Avoid acquiring nested locks
    // Critical section
}
```
✅ 4. Use a Deadlock Detection Tool
Use Java Thread Dump (jstack) or profiling tools like VisualVM to detect deadlocks.

jstack <pid>

🚀 Final Recommendation
Approach	Effectiveness
Lock Ordering	✅ Highly effective
Using tryLock()	✅ Prevents deadlocks
Avoid Nested Locks	✅ Reduces risk
Thread Dump Analysis	✅ Helps detection


##  What is the difference between the start() and run() method?

First, both methods are operated in general over the thread. So if we do use threadT1.start() then this method will look for the run() method to create a new thread. 
While in case of theadT1.run() method will be executed just likely the normal method by the “Main” thread without the creation of any new thread.

### Serialization 
Serialization is like saving an object as a file or converting it into a format that can be stored or sent over a network.

Imagine you have a Toy (Java Object), and you want to pack it into a box (Convert to a file or stream) so you can store it or send it to someone. Later, you can unpack (Deserialize) it to get back the same toy (Java Object).

## Where is Serialization Used in a Spring Boot Project?
Serialization is widely used in Spring Boot for various functionalities, such as caching, session management, database operations, and messaging. 

## 🔹 Summary: Where Serialization is Used in Spring Boot
Caching (Redis, Ehcache)	Objects are stored in cache
Session Management	Sessions need to be shared across instances
Message Queues (Kafka, RabbitMQ)	Objects need to be sent as messages
REST APIs (Jackson, JSON/XML)	Objects are converted to JSON/XML
Database ORM (JPA/Hibernate)	Objects with non-standard fields need serialization
File Storage (S3, Local, NFS)	Objects are stored as files

### What is Deserialization?

Deserialization is the reverse of serialization—it converts stored or transmitted data (bytes, JSON, XML, etc.) back into a Java object.


## 📌 Where is Deserialization Used in Spring Boot?
Use Case	How Deserialization Happens?
REST APIs (JSON/XML to Object)	Jackson converts JSON/XML into Java objects
Message Queues (Kafka, RabbitMQ)	Messages are deserialized into Java objects
Caching (Redis, Ehcache)	Cached objects are deserialized before use
Session Management	Session attributes are deserialized when restored
Database ORM (JPA/Hibernate)	Objects are deserialized when retrieved from DB
File Storage (S3, Local, NFS)	Stored objects are deserialized when loaded


### String Pool in Java

The String Pool in Java is a special memory area inside the heap called the String Constant Pool (SCP) or String Intern Pool. It optimizes memory usage by storing only one copy of each unique string literal, reducing redundancy and improving performance.

# 1. How String Pool Works?

When a string literal (e.g., "Hello") is created, Java first checks the String Pool.
If the literal already exists in the pool, Java returns a reference to the existing object.
If it does not exist, Java creates a new string in the pool.

# 2. Example of String Pool Behavior

public class StringPoolExample {
    public static void main(String[] args) {
        String s1 = "Hello";
        String s2 = "Hello";

        System.out.println(s1 == s2);  // true (Same reference from the String Pool)
    }
}
Both s1 and s2 point to the same object in the String Pool.

# 3. String Pool and new Keyword
When you create a String using new, it creates a new object in the heap, not in the pool.


public class StringHeapExample {
    public static void main(String[] args) {
        String s1 = "Hello";  
        String s2 = new String("Hello");

        System.out.println(s1 == s2);  // false (Different memory locations)
    }
}
"Hello" is stored in the String Pool.
new String("Hello") creates a new object in the heap.

# 4. Using intern() to Add Strings to the Pool
If you create a string using new, but want to store it in the pool, use .intern():

public class StringInternExample {
    public static void main(String[] args) {
        String s1 = new String("Java");
        String s2 = s1.intern(); // Moves "Java" to the String Pool

        String s3 = "Java";

        System.out.println(s2 == s3);  // true (Both refer to the same pool object)
    }
}

# 5. Benefits of the String Pool
✅ Memory Efficiency – Avoids storing duplicate string objects.
✅ Performance Optimization – Reusing immutable strings reduces object creation overhead.
✅ Thread-Safety – Strings are immutable and safely shared across threads.

# 6. When Should You Avoid String Pool?
If you have many dynamic strings (e.g., reading from files or databases), storing them in the pool can cause memory overhead.
Use new String(value) to avoid pooling if necessary.

### How do you handle deadlocks in Java?

A deadlock occurs in a multithreaded program when two or more threads are waiting for each other's resources, preventing further execution.


## Handling Deadlocks in Java


1️⃣ Avoid Nested Locks

2️⃣ Use Try-Lock Instead of Synchronized

3️⃣ Implement a Lock Ordering Mechanism
Always acquire locks in a consistent order to avoid circular waiting.

4️⃣ Set Timeouts for Locks
Using tryLock(timeout, TimeUnit) in ReentrantLock to avoid indefinite waiting.

5️⃣ Detect and Recover from Deadlocks


##  What is a ThreadPool in Java?
A ThreadPool is a pool of pre-created threads that can be reused to execute multiple tasks instead of creating new threads for each task. It helps manage a large number of threads efficiently.

Java provides the Executor Framework (java.util.concurrent.Executors) to manage thread pools.


### 🔹 What is ExecutorService ?

ExecutorService is a special thread manager in Java that helps you run multiple tasks in the background using a pool of threads. Instead of manually creating and managing threads, ExecutorService does it for you efficiently.

Think of it as a task manager:

You submit tasks, and it assigns them to available threads.
It reuses threads instead of creating new ones for every task.
It ensures better performance by managing threads automatically.

## 🔹 Why Use ExecutorService?

✅ Easy to use – No need to manually create and start threads.
✅ Improves performance – Reuses threads instead of constantly creating/destroying them.
✅ Prevents resource exhaustion – Limits the number of running threads.
✅ Supports scheduling – Can run tasks periodically.


## 🔹 How to Use ExecutorService?

1️⃣ Create a Thread Pool
```
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;

public class ExecutorServiceExample {
    public static void main(String[] args) {
        ExecutorService executor = Executors.newFixedThreadPool(3); // 3 threads in the pool

        for (int i = 1; i <= 5; i++) {
            final int taskNumber = i;
            executor.execute(() -> {
                System.out.println("Task " + taskNumber + " executed by " + Thread.currentThread().getName());
            });
        }

        executor.shutdown(); // Gracefully shuts down after all tasks are done
    }
}
```
## 🔹 What happens here?

We create a thread pool with 3 threads.

We submit 5 tasks → They run in the available threads.

The executor.shutdown() ensures tasks complete before shutting down.


### 🔹 Different Types of ExecutorService
1️⃣ Fixed Thread Pool – Limits the number of threads.

ExecutorService executor = Executors.newFixedThreadPool(3);

2️⃣ Cached Thread Pool – Creates new threads as needed but reuses idle threads.

ExecutorService executor = Executors.newCachedThreadPool();

3️⃣ Single Thread Executor – Runs tasks one at a time in a single thread.

ExecutorService executor = Executors.newSingleThreadExecutor();

4️⃣ Scheduled Thread Pool – Runs tasks after a delay or at fixed intervals.


ScheduledExecutorService scheduler = Executors.newScheduledThreadPool(2);


## Volatile Keyword


The volatile keyword in Java is used for thread safety when accessing shared variables in a multithreaded environment. It ensures that:

Visibility: Changes made by one thread are immediately visible to other threads.
Prevents Caching: The variable is always read from and written to main memory, not from CPU caches.
Atomicity for Reads/Writes: Ensures that reads and writes are atomic for variables (but not compound operations like count++).

###  Marker Interface in Java
A Marker Interface is an interface that does not contain any methods or fields. It is used to signal or "mark" a class, providing metadata that can be used by the Java runtime or frameworks to apply special behavior.

# Examples of Marker Interfaces in Java
Some built-in marker interfaces in Java include:

Serializable (Java I/O) – Marks objects that can be serialized.
Cloneable (Java Object Cloning) – Marks objects that can be cloned using clone().
Remote (Java RMI) – Marks objects that can be used for remote method invocation (RMI).

### ✅ Why Use @FunctionalInterface?
Ensures the interface has only ONE abstract method
If a developer accidentally adds a second method, the compiler will throw an error.
Improves code readability and clarity
Clearly tells other developers that the interface is intended for functional programming.
Enables lambda expressions
Functional interfaces are essential for Java’s functional programming features, especially in Java 8 and later.

### Serializable in Java

The Serializable interface in Java is a marker interface (i.e., it has no methods) that allows an object to be converted into a byte stream so that it can be saved to a file, sent over a network, or stored in a database.

When an object is serialized, its state (i.e., its fields) is converted into a format that can be deserialized (restored) later.

## Why Use Serializable?
To save an object's state to a file or database.
To send objects over a network (e.g., in RMI, HTTP requests, or WebSockets).
To cache objects for faster access in future requests.
To share objects between different JVMs.

## How to Make a Class Serializable?
Simply implement java.io.Serializable, since it's a marker interface (no methods to implement).

## serialVersionUID – Why Is It Important?
serialVersionUID is a unique identifier that ensures version compatibility during deserialization.
If you modify a class after serialization (e.g., add/remove fields), deserialization will fail unless serialVersionUID remains the same.


###  transient Keyword in Java
The transient keyword in Java is used to prevent a field from being serialized when an object is converted into a byte stream.

When a field is declared transient, it is ignored during serialization, meaning it won't be saved and will have its default value when deserialized.

## Why Use transient?
Protect sensitive data – e.g., passwords, security keys.
Avoid unnecessary serialization – e.g., cache data, large objects.
Prevent serialization errors – e.g., when a field is not Serializable.

### Java memory is divided into the following key regions:

Heap Memory	Stores objects and instance variables (shared across threads).

Stack Memory	Stores method-local variables, method calls, and references (per thread).

Method Area (MetaSpace in Java 8+)	Stores class metadata, static variables, and constants.

PC Register	Holds the address of the currently executing instruction.

Native Method Stack	Used for native (JNI) method execution.

### Can We Override a Static Method in Java?
No, we cannot override a static method in Java. However, we can hide a static method in the child class.

###   Why Can't We Override Static Methods?
Method Overriding is Based on Dynamic Binding (Runtime Polymorphism)

1) Static methods belong to the class, not the instance.
Overriding works with instances, but static methods do not depend on objects.
Static Methods are Resolved at Compile-Time

2) Method overriding depends on dynamic method dispatch (runtime binding).
Static methods are resolved at compile-time, so method calls are bound to the class, not the object.


###  How to Create a Thread in Java?
In Java, we can create a thread using two main approaches:

a) Extending the Thread class
b) Implementing the Runnable interface
c)  Creating a Thread Using a Lambda Expression (Java 8+)
Since Runnable is a functional interface, we can use a lambda expression to create a thread in a shorter way.

✅ Example
```
public class LambdaThreadExample {
    public static void main(String[] args) {
        Thread t1 = new Thread(() -> {
            System.out.println("Thread using lambda is running... " + Thread.currentThread().getName());
        });

        t1.start();
    }
}
```
d)Creating a Thread Using an Executor Service (ThreadPool)

Instead of manually creating threads, we can use a thread pool for efficient thread management.

✅ Example: Using ExecutorService
```
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;

public class ThreadPoolExample {
    public static void main(String[] args) {
        ExecutorService executor = Executors.newFixedThreadPool(3);

        for (int i = 0; i < 5; i++) {
            executor.execute(() -> {
                System.out.println("Thread from pool: " + Thread.currentThread().getName());
            });
        }

        executor.shutdown();
    }
}
```

## Which Approach Should You Use?

| Method                   | Use Case                                                                                                                       |
|--------------------------|--------------------------------------------------------------------------------------------------------------------------------|
| Extending Thread         | When you need to create a thread by inheriting the Thread class (but not recommended due to lack of flexibility).               |
| Implementing Runnable    | Preferred method, as Java supports only single inheritance, and we can implement multiple interfaces.                          |
| Lambda Expression        | Best for short and simple thread tasks (Java 8+).                                                                               |
| Executor Service (ThreadPool) | Best when managing multiple threads efficiently, especially in production.                                                  |



## Kafka Architecture

| Component       | Description                                                                           |
|-----------------|---------------------------------------------------------------------------------------|
| Producer        | Sends messages (events) to Kafka topics.                                              |
| Broker          | Kafka server that stores and manages messages.                                        |
| Topic           | Logical name where messages are stored (similar to a table in databases).             |
| Partition       | A topic is divided into partitions for scalability.                                   |
| Consumer        | Reads messages from topics.                                                           |
| Consumer Group  | A group of consumers that parallelly consume messages from a topic.                   |
| Zookeeper       | Manages Kafka metadata, leader election, and configuration.                           |





















