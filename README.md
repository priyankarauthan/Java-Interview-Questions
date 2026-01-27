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

# Java Garbage Collection (GC)

Garbage Collection (GC) in Java is an automatic memory management process that removes unused objects from memory, preventing memory leaks and optimizing performance.

## 📌 How Garbage Collection Works
Java uses **automatic memory management**, where the JVM tracks and removes objects that are no longer reachable.


### Heap Structure (Very Important ⭐)

Java heap is divided into:

Young Generation
  ├── Eden
  ├── Survivor S0
  └── Survivor S1

Old Generation (Tenured)

### Young Generation

New objects are created in Eden

Minor GC happens here

Objects that survive move to Survivor spaces

After multiple GCs → moved to Old Generation

### Old Generation

Stores long-living objects

Major GC / Full GC happens here

Slower than Minor GC

### 🔄 Phases of Garbage Collection
1. **Mark** – Identifies all objects still in use.
2. **Sweep** – Deletes objects that are no longer reachable.
3. **Compact** – Moves remaining objects together to optimize memory allocation.

## 🔥 JVM Memory Areas and GC
Java memory is divided into different regions:

### **1. Young Generation**
- Contains newly created objects.
- Uses **Minor GC** to clean up unused objects quickly.
- Divided into:
  - **Eden Space** (new objects)
  - **Survivor Spaces (S0, S1)** (objects that survived Minor GC)

### **2. Old Generation (Tenured Generation)**
- Stores long-lived objects.
- Uses **Major (Full) GC**.

### **3. Metaspace (Java 8+)**
- Stores class metadata.
- Unlike **PermGen** (Java 7 and below), it can grow dynamically.

## ⚡ Types of Garbage Collectors
Java provides multiple GC implementations to optimize performance:

| Garbage Collector | Description | JVM Option |
|------------------|-------------|-------------|
| **Serial GC** | Single-threaded, best for small applications | `-XX:+UseSerialGC` |
| **Parallel GC** | Multi-threaded, best for multi-core systems | `-XX:+UseParallelGC` |
| **CMS (Concurrent Mark-Sweep) GC** | Reduces pause time, suitable for low-latency applications (deprecated in Java 9+) | `-XX:+UseConcMarkSweepGC` |
| **G1 (Garbage First) GC** | Divides heap into regions, balances throughput and latency | `-XX:+UseG1GC` |
| **ZGC (Java 11+)** | Ultra-low latency, handles large heaps efficiently | `-XX:+UseZGC` |
| **Shenandoah GC (Java 12+)** | Performs concurrent compaction with low-pause times | `-XX:+UseShenandoahGC` |

## 🛠 Forcing Garbage Collection
Though GC is automatic, you can suggest it using:
```java
System.gc();  // Requests GC (not guaranteed)
Runtime.getRuntime().gc();
```
However, JVM **decides when to run GC**, so explicit calls are **not recommended**.

## 🚀 Best Practices for Efficient GC
- Use **object pooling** for expensive objects.
- Set unused object references to `null`.
- Avoid **memory leaks** (e.g., static references, unclosed resources).
- Optimize **GC tuning parameters** based on profiling.
- Use **profiling tools** like VisualVM, JConsole, Eclipse Memory Analyzer.


  
## How Does Garbage Collection Work?

Objects are allocated in heap memory when created using new.

Garbage Collector identifies unreachable objects (objects with no active references).

GC removes those objects and reclaims memory.

Memory is compacted and optimized to reduce fragmentation.

## When Does Garbage Collection Happen?

The JVM runs GC automatically when it detects low memory.

You can suggest GC using System.gc(), but it is not guaranteed to run immediately.

GC primarily occurs during idle CPU time to minimize performance impact.


### JVM Memory Areas (Quick Map)
┌───────────────┐
│   Stack       │ → Local variables, method calls
├───────────────┤
│   Heap        │ → Objects, instance variables
├───────────────┤
│ Method Area   │ → Class metadata, static variables
│ (Metaspace)  │
├───────────────┤
│ PC Register   │
├───────────────┤
│ Native Stack  │
└───────────────┘

### String/ StringBuilder/ StringBuffer

String → Immutable and thread-safe

StringBuilder → Mutable and fast, not thread-safe

StringBuffer → Mutable and thread-safe but slower


## How to Make a Class Immutable in Java?

An immutable class is a class whose objects cannot be modified after creation. All fields of an immutable object remain constant throughout its lifecycle.

Steps to Create an Immutable Class in Java

To make a class immutable, follow these best practices:

#### 1. Declare the class as final
   
This prevents subclassing, which could allow modification of fields.

final class ImmutableClass {

#### 2. Make all fields private and final

Fields should be private to prevent direct access and final to ensure they are assigned only once.

private final int id;
private final String name;

#### 3. Initialize fields via a constructor

Provide a constructor to initialize all fields.

public ImmutableClass(int id, String name) {
    this.id = id;
    this.name = name;
}

#### 4. Do not provide setter methods

Setters allow modification, so they should not be present.

#### 5. Return deep copies of mutable fields in getters
   
If a field is a mutable object (like a List or Date), return a defensive copy to prevent modification.

public Date getDate() {
    return new Date(date.getTime());  // Defensive copy
}


Here's how you can design one properly:-

```
public final class Person {
    private final String name;
    private final int age;
    private final List<String> hobbies;

    public Person(String name, int age, List<String> hobbies) {
        this.name = name;
        this.age = age;
        // Defensive copy to avoid external mutation
        this.hobbies = hobbies == null ? List.of() : new ArrayList<>(hobbies);
    }

    public String getName() {
        return name;
    }

    public int getAge() {
        return age;
    }

    public List<String> getHobbies() {
        // Return a copy to preserve immutability
        return new ArrayList<>(hobbies);
    }
}
```




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

#### 🔹 1. Inheritance (IS-A Relationship)

Type: Generalization / Specialization

Meaning: One class is a subtype of another.

Direction: Unidirectional (child → parent)

Example: Dog IS-A Animal

Use Case: Code reuse, polymorphism

```
class Animal {}
class Dog extends Animal {}
```
#### 🔹 2. Association (HAS-A Relationship)
Type: Structural

Meaning: One class uses or refers to another.

Direction: Unidirectional or Bidirectional

Example: Student associated with College

```
class College {}
class Student {
    private College college;
}
```
### 🔹 3. Aggregation (HAS-A but Weak Relationship)
Type: Whole-Part (weak)

Meaning: One class contains another, but both can exist independently.

Direction: Usually Unidirectional

Example: University has Departments

```
class Department {}
class University {
    private List<Department> departments;
}
```
### 🔹 4. Composition (HAS-A and Strong Relationship)
Type: Whole-Part (strong)

Meaning: One class owns another. Lifecycles are tied.

Direction: Unidirectional

Example: Library contains Books. If Library is deleted, Books are too.

```
class Book {}
class Library {
    private List<Book> books = new ArrayList<>();
}
```

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
Serialization in Java is the process of converting an object into a byte stream so that it can be persisted to a file, sent over a network, or stored in a database. Deserialization is the reverse process, where the byte stream is converted back into an object.

Imagine you have a Toy (Java Object), and you want to pack it into a box (Convert to a file or stream) so you can store it or send it to someone. Later, you can unpack (Deserialize) it to get back the same toy (Java Object).

### transient Keyword:

Fields marked as transient are not serialized.
### serialVersionUID:

A unique identifier to maintain version compatibility during deserialization.

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

### 1️⃣ @Mock
Purpose: Creates a mock instance of a class or interface.
Usage: Used to mock dependencies of the class being tested.
Behavior: Does not call real methods but returns stubbed/mock responses.
Example
```
@Mock
private UserRepository userRepository;
```

🔹 This tells Mockito to create a fake instance of UserRepository (without hitting the real database).

### 2️⃣ @InjectMocks

Purpose: Injects mocked dependencies into a real instance of a class.
Usage: Used for the class under test.
Behavior: Calls real methods of the main class, but mocked dependencies return stubbed values.
Example
```
@InjectMocks
private UserService userService;
```
🔹 This tells Mockito to inject the mocked UserRepository into UserService

## Default Behavior of equals() in String Class
Unlike objects that inherit equals() from Object, 
the String class overrides equals() to compare character sequences instead of memory references.

## Does equals() Check Value for Strings in Java? 🤔
✅ Yes!
For Strings, equals() checks the actual value (content), not the memory reference.

## Does equals() Check Memory Reference in Java? 🤔
It depends on whether equals() is overridden or not.✅
Since equals() is not overridden, it behaves like == and checks memory references.
Default equals() in Object
```
public boolean equals(Object obj) {
    return (this == obj);  // Memory reference check
}
```

### Deadlock Prevention

Deadlocks occur when multiple threads acquire locks in different orders.
The solution is to always acquire locks in a fixed order to prevent circular waiting.
synchronized(LOCK1) first, then synchronized(LOCK2) ensures safe locking.


### default Keyword in Java 8

In Java 8, the default keyword is used to define default methods in interfaces. This allows interfaces to have method implementations without breaking existing implementing classes.

## Why Default Methods?

Before Java 8, interfaces could only have abstract methods. If a new method was added to an interface, all implementing classes had to implement it. Default methods solve this problem by allowing new methods with a default implementation.

1)Backward Compatibility

Allows adding new methods to interfaces without breaking existing implementations.

2)  Multiple Inheritance Issue
   
If a class implements two interfaces with the same default method, Java forces an override to resolve ambiguity.

### GENERICS

Generics in Java are like templates that let you write code that works with different types of data without having to rewrite the same code for each type. They make your code more flexible, reusable, and type-safe.

### What is meant by asynchronous programming?

Asynchronous programming is a programming paradigm that allows a program to initiate tasks that run separately from the main application thread, enabling the program to continue executing other operations without waiting for these tasks to complete. This approach enhances efficiency, especially in applications that perform time-consuming operations like file I/O, network requests, or database queries.


### CompletableFuture 

It is a class introduced in Java 8 that facilitates asynchronous programming. It allows you to run tasks in the background without blocking the main thread, enabling your application to perform other operations simultaneously.

# Key Features:

Asynchronous Execution: Run tasks independently without waiting for them to complete.

Task Chaining: Link multiple tasks to run in sequence or in parallel, where each task can start after the previous one finishes.

Manual Completion: Explicitly complete a task and provide its result when it's ready.

Exception Handling: Gracefully manage errors that occur during asynchronous operations.

Simple Example:-

Here's how you can use CompletableFuture to run a task asynchronously:

```
import java.util.concurrent.CompletableFuture;

public class CompletableFutureExample {
    public static void main(String[] args) {
        // Run a task asynchronously
        CompletableFuture<Void> future = CompletableFuture.runAsync(() -> {
            // Simulate a long-running task
            try {
                Thread.sleep(1000);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            System.out.println("Task completed!");
        });

        // Continue with other tasks...

        // Wait for the asynchronous task to complete
        future.join();
    }
}
```
In this example, the task runs in the background, allowing the main thread to proceed with other operations. The join() method ensures that the main thread waits for the asynchronous task to finish before exiting.

By using CompletableFuture, you can write more efficient and responsive applications that handle multiple tasks concurrently without unnecessary delays.

### Fork in Java

In Java, the concept of "forking" is implemented through the Fork/Join framework, introduced in Java 7 as part of the java.util.concurrent package. This framework facilitates parallel execution by recursively dividing tasks into smaller subtasks (forking) and then combining their results (joining), aligning with the fork–join model of parallel computing. 


### Key Components of the Fork/Join Framework:

### ForkJoinPool:

A specialized implementation of ExecutorService designed to manage and execute tasks that can be broken down into subtasks. It utilizes a work-stealing algorithm to efficiently distribute tasks among available threads. 


### ForkJoinTask: 

An abstract class representing a task that can be subdivided into smaller tasks. Subclasses include RecursiveTask (which returns a result) and RecursiveAction (which does not return a result).

### THRED SAFE DATA STRUCTURES

#### 1. Concurrent Collections (from java.util.concurrent)

ConcurrentHashMap — thread-safe version of HashMap.

ConcurrentSkipListMap — thread-safe version of TreeMap (sorted map).

ConcurrentSkipListSet — thread-safe version of TreeSet.

CopyOnWriteArrayList — thread-safe version of ArrayList (optimized for more reads, fewer writes).

CopyOnWriteArraySet — thread-safe Set based on CopyOnWriteArrayList.

LinkedBlockingQueue — thread-safe queue for producer-consumer patterns (FIFO).

ArrayBlockingQueue — bounded, thread-safe queue.

PriorityBlockingQueue — unbounded, thread-safe priority queue.

DelayQueue — queue where elements become available after a delay.

SynchronousQueue — queue where insert and remove operations must happen simultaneously.

LinkedTransferQueue — highly scalable, non-blocking queue.

ConcurrentLinkedQueue — non-blocking, thread-safe queue (FIFO).

ConcurrentLinkedDeque — non-blocking, thread-safe double-ended queue.

#### 2. Synchronized Wrappers (from Collections.synchronizedXxx)

(These provide coarse-grained synchronization — full method locking.)

Collections.synchronizedList(List list) — wraps any list (e.g., ArrayList, LinkedList).

Collections.synchronizedMap(Map map) — wraps any map (e.g., HashMap).

Collections.synchronizedSet(Set set) — wraps any set.

Collections.synchronizedSortedMap(SortedMap map) — wraps TreeMap.

Collections.synchronizedSortedSet(SortedSet set) — wraps TreeSet.

#### 3. Legacy Thread-Safe Classes (built-in synchronization)

Vector — thread-safe dynamic array (older, slower, replaced by CopyOnWriteArrayList usually).

Hashtable — thread-safe hash map (older, slower, replaced by ConcurrentHashMap).

Stack — thread-safe (extends Vector).

#### 4. Atomic Variables and Structures (from java.util.concurrent.atomic)

AtomicInteger, AtomicLong, AtomicBoolean, AtomicReference — thread-safe primitive wrappers.

AtomicIntegerArray, AtomicLongArray — thread-safe arrays.

AtomicReferenceArray — thread-safe reference arrays.

#### 5. Special Thread-Safe Data Structures

Phaser, CyclicBarrier, CountDownLatch — for synchronization (though not exactly "data structures," still important).

StampedLock — used for more efficient read/write locks compared to ReentrantReadWriteLock (not a data structure, but can make structures thread-safe with careful design).




### ✅ Checked Exceptions (must be handled or declared)

These extend `Exception` but not `RuntimeException`.

| Exception Type              | Description                                      |
|----------------------------|--------------------------------------------------|
| `IOException`              | Error during input/output operations             |
| `FileNotFoundException`    | File not found during file read operation        |
| `SQLException`             | Issues with database access                      |
| `ParseException`           | Error while parsing data (e.g., dates)           |
| `ClassNotFoundException`   | When a class is not found at runtime             |
| `InterruptedException`     | When a thread is interrupted                     |
| `CloneNotSupportedException` | Thrown when `clone()` is not supported         |

✅ Checked exceptions occur at:
⏱️ Compile Time

📌 Explanation:
Checked exceptions are checked by the Java compiler.

If your code throws a checked exception, the compiler forces you to either:

Handle it using try-catch, or

Declare it using throws in the method signature.

If you do neither, your code will not compile.



### What does synchronized do?

It ensures that only ONE thread at a time can execute a particular block of code or method on the same object (lock).



---

### ❌ Unchecked Exceptions (no need to handle, runtime error)

These extend `RuntimeException`.

| Exception Type                   | Description                                     |
|----------------------------------|-------------------------------------------------|
| `NullPointerException`           | Accessing members on a null object              |
| `ArrayIndexOutOfBoundsException` | Accessing invalid index in array                |
| `ArithmeticException`            | Division by zero                                |
| `IllegalArgumentException`       | Invalid argument passed to a method             |
| `NumberFormatException`          | Parsing a string that is not a valid number     |
| `IllegalStateException`          | Method called at wrong time/state               |
| `ClassCastException`             | Invalid casting between types                   |


### How to check if two objects are equal?

```
class Student {
    int id;
    String name;

    Student(int id, String name) {
        this.id = id;
        this.name = name;
    }

    @Override
    public boolean equals(Object o) {

        if (this == o) return true;     // same object
        if (o == null) return false;    // null check
        if (getClass() != o.getClass()) return false;

        Student s = (Student) o;

        return id == s.id &&
               Objects.equals(name, s.name);
    }
}
```

# SOLID Principles

SOLID is a set of five object-oriented design principles that help make software
more maintainable, flexible, and scalable.

---

## S — Single Responsibility Principle (SRP)

**Definition:**  
A class should have only one reason to change.

**Explanation:**  
Each class should focus on a single responsibility or job. If a class handles
multiple responsibilities, changes in one area may affect others.

**Benefits:**
- Easier to understand
- Easier to maintain
- Reduced risk of bugs

---

## O — Open/Closed Principle (OCP)

**Definition:**  
Software entities should be open for extension but closed for modification.

**Explanation:**  
You should be able to add new functionality without changing existing code.
This is usually achieved using inheritance, interfaces, or composition.

**Benefits:**
- Reduces risk of breaking existing code
- Encourages reusable and scalable designs

---

## L — Liskov Substitution Principle (LSP)

**Definition:**  
Subtypes must be substitutable for their base types without affecting correctness.

**Explanation:**  
Objects of a superclass should be replaceable with objects of its subclasses
without breaking the application.

**Benefits:**
- Ensures reliable inheritance
- Prevents unexpected runtime behavior

---

## I — Interface Segregation Principle (ISP)

**Definition:**  
Clients should not be forced to depend on interfaces they do not use.

**Explanation:**  
Instead of one large interface, create smaller, more specific interfaces so
classes only implement what they actually need.

**Benefits:**
- Reduces unnecessary dependencies
- Improves code clarity and flexibility

---

## D — Dependency Inversion Principle (DIP)

**Definition:**  
High-level modules should not depend on low-level modules.  
Both should depend on abstractions.

**Explanation:**  
Depend on interfaces or abstract classes rather than concrete implementations.
Details should depend on abstractions, not the other way around.

**Benefits:**
- Loose coupling
- Easier testing
- Easier to replace implementations

---

## Summary

| Principle | Focus |
|---------|-------|
| SRP | One responsibility per class |
| OCP | Extend behavior without modifying code |
| LSP | Correct inheritance and substitution |
| ISP | Small, focused interfaces |
| DIP | Depend on abstractions, not implementations |

---

### Method Hiding

Method hiding occurs when a subclass defines a static method with the same signature as a static method in the parent class or interface. Static methods are resolved at compile time, so polymorphism does not apply.

```
interface Payment {

    static void info() {
        System.out.println("Payment interface static method");
    }
}

class UpiPayment implements Payment {

    static void info() {
        System.out.println("UPI info");
    }
}

public class PaymentTest {
    public static void main(String[] args) {

        Payment.info();       // Payment version
        UpiPayment.info();    // UpiPayment version
        // correct way
    }
}

```





































